---
title: 'Azure AD Connect-Synchronisierung:  Ändern des Azure AD Connect-Synchronisierungsdienstkontos | Microsoft-Dokumentation'
description: Dieses Themendokument beschreibt den Verschlüsselungsschlüssel und führt aus, wie Sie ihn nach dem Ändern des Kennworts verwerfen können.
services: active-directory
keywords: Azure AD-Synchronisierungsdienstkonto, Kennwort
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15d0d537a23e21eeda3b284e7ec706cde2b443e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014076"
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Ändern des Kennworts für das Azure AD Connect-Synchronisierungsdienstkonto
Wenn Sie das Kennwort für das Dienstkonto der Azure AD Connect-Synchronisierung ändern, kann der Synchronisierungsdienst nicht ordnungsgemäß gestartet werden, bis der Verschlüsselungsschlüssel verworfen und das Kennwort für das Dienstkonto der Azure AD Connect-Synchronisierung erneut initialisiert wurde. 

Azure AD Connect verwendet als Teil der Synchronisierungsdienste einen Verschlüsselungsschlüssel zum Speichern der Kennwörter für die AD DS- und Azure AD-Dienstkonten.  Diese Konten werden verschlüsselt, bevor sie in der Datenbank gespeichert werden. 

Der verwendete Verschlüsselungsschlüssel wird über [Windows-Datenschutz (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx) gesichert. DPAPI schützt den Verschlüsselungsschlüssel mithilfe des **Kennworts für das Azure AD Connect-Synchronisierungsdienstkonto**. 

Wenn Sie das Kennwort des Dienstkontos ändern müssen, können Sie hierzu die Verfahren unter [Verwerfen des Verschlüsselungsschlüssels für die Azure AD Connect-Synchronisierung](#abandoning-the-azure-ad-connect-sync-encryption-key) verwenden.  Diese Verfahren sollten auch verwendet werden, wenn Sie den Verschlüsselungsschlüssel aus irgendeinem Grund verwerfen müssen.

## <a name="issues-that-arise-from-changing-the-password"></a>Durch das Ändern des Kennworts entstehende Probleme
Es gibt zwei Dinge, die beim Ändern des Kennwort für das Dienstkonto durchgeführt werden müssen.

Zunächst müssen Sie das Kennwort im Windows-Dienststeuerungs-Manager ändern.  Bis dieses Problem behoben ist, werden Ihnen folgende Fehler angezeigt:


- Wenn Sie versuchen, den Synchronisierungsdienst im Windows-Dienststeuerungs-Manager zu starten, erhalten Sie den Fehler **Der Microsoft Azure AD-Synchronisierungsdienst konnte auf dem lokalen Computer nicht gestartet werden**. **Fehler 1069: Der Dienst wurde aufgrund eines Anmeldefehlers nicht gestartet.**
- In der Windows-Ereignisanzeige enthält das Systemereignisprotokoll einen Fehler mit der **Ereignis-ID 7038** und die Meldung „**Der ADSync-Dienst konnte sich mit dem aktuellen Kennwort aufgrund des folgenden Fehlers nicht anmelden: Der Benutzername oder das Kennwort ist falsch.**“

Zweitens kann der Synchronisierungsdienst unter bestimmten Bedingungen bei Aktualisierung des Kennworts nicht mehr über DPAPI auf den Verschlüsselungsschlüssel zugreifen. Ohne den Verschlüsselungsschlüssel kann der Synchronisierungsdienst die Kennwörter nicht entschlüsseln, die zum Synchronisieren nach/aus dem lokalen AD und Azure AD erforderlich sind.
Ihnen werden beispielsweise folgende Fehler angezeigt:

- Wenn Sie im Windows-Dienststeuerungs-Manager versuchen, den Synchronisierungsdienst zu starten, und der Verschlüsselungsschlüssel nicht abgerufen werden kann, erhalten Sie die Fehlermeldung <strong>Der Microsoft Azure AD-Synchronisierungsdienst konnte auf dem lokalen Computer nicht gestartet werden. Weitere Informationen finden Sie im Systemereignisprotokoll. Setzen Sie sich mit dem Diensthersteller in Verbindung, wenn es sich um einen Nicht-Microsoft-Dienst handelt. Beziehen Sie sich auf den dienstspezifischen Fehlercode -21451857952</strong>.
- In der Windows-Ereignisanzeige enthält das Anwendungsereignisprotokoll einen Fehler mit der **Ereignis-ID 6028** und die Fehlermeldung *„Auf den Serververschlüsselungsschlüssel kann nicht zugegriffen werden“*.

Um sicherzustellen, dass Sie diese Fehler nicht erhalten, führen Sie beim Ändern des Kennworts die Verfahren unter [Verwerfen des Verschlüsselungsschlüssels der Azure AD Connect-Synchronisierung](#abandoning-the-azure-ad-connect-sync-encryption-key) aus.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Verwerfen des Verschlüsselungsschlüssels der Azure AD Connect-Synchronisierung
>[!IMPORTANT]
>Die folgenden Verfahren gelten nur für Azure AD Connect Build 1.1.443.0 oder früher.

Verwenden Sie die folgenden Verfahren, um den Verschlüsselungsschlüssel zu verwerfen.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>So verwerfen Sie den Verschlüsselungsschlüssel

Wenn Sie den Verschlüsselungsschlüssel verwerfen müssen, verwenden Sie dazu die folgenden Verfahren.

1. [Beenden des Synchronisierungsdiensts](#stop-the-synchronization-service)

1. [Verwerfen des vorhandenen Verschlüsselungsschlüssels](#abandon-the-existing-encryption-key)

2. [Angeben des Kennworts für das AD DS-Konto](#provide-the-password-of-the-ad-ds-account)

3. [Erneutes Initialisieren des Kennworts für das Azure AD-Synchronisierungskonto](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Starten des Synchronisierungsdiensts](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Beenden des Synchronisierungsdiensts
Zunächst können Sie den Dienst im Dienststeuerungs-Manager von Windows beenden.  Stellen Sie sicher, dass der Dienst nicht ausgeführt wird, wenn Sie versuchen, ihn zu beenden.  Wenn er ausgeführt wird, warten Sie, bis er abgeschlossen ist, und beenden Sie ihn dann.


1. Wechseln Sie zum Windows-Dienststeuerungs-Manager („START“ → „Dienste“).
2. Wählen Sie **Microsoft Azure AD-Synchronisierung** aus, und klicken Sie auf „Beenden“.

#### <a name="abandon-the-existing-encryption-key"></a>Verwerfen des vorhandenen Verschlüsselungsschlüssels
Verwerfen Sie den vorhandenen Verschlüsselungsschlüssel, damit der neue Verschlüsselungsschlüssels erstellt werden kann:

1. Melden Sie sich bei Ihrem Azure AD Connect-Server als Administrator an.

2. Starten Sie eine neue PowerShell-Sitzung.

3. Navigieren Sie zum Ordner `$env:Program Files\Microsoft Azure AD Sync\bin\`.

4. Führen Sie den folgenden Befehl aus: `./miiskmu.exe /a`

![Hilfsprogramm für den Verschlüsselungsschlüssel der Azure AD Connect-Synchronisierung](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Angeben des Kennworts für das AD DS-Konto
Da die in der Datenbank gespeicherten vorhandenen Kennwörter nicht mehr entschlüsselt werden können, müssen Sie dem Synchronisierungsdienst das Kennwort des AD DS-Kontos bereitstellen. Der Synchronisierungsdienst verschlüsselt die Kennwörter mithilfe des neuen Verschlüsselungsschlüssels:

1. Starten Sie den Synchronization Service Manager („START“ > „Synchronisierungsdienst“).
</br>![Synchronization Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Wechseln Sie zur Registerkarte **Connectors**.
3. Wählen Sie den **AD-Connector** aus, der Ihrem lokalen AD entspricht. Wenn Sie mehrere AD-Connectors verwenden, wiederholen Sie die folgenden Schritte für jeden von ihnen.
4. Klicken Sie unter **Aktionen** auf **Eigenschaften**.
5. Wählen Sie im Popupdialogfeld **Mit Active Directory-Gesamtstruktur verbinden** aus:
6. Geben Sie das Kennwort des AD DS-Kontos im Textfeld **Kennwort** ein. Wenn Sie das Kennwort nicht kennen, müssen Sie es auf einen bekannten Wert festlegen, bevor Sie diesen Schritt ausführen.
7. Klicken Sie auf **OK**, um das neue Kennwort zu speichern und das Popupdialogfeld zu schließen.
![Hilfsprogramm für den Verschlüsselungsschlüssel der Azure AD Connect-Synchronisierung](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Erneutes Initialisieren des Kennworts für das Azure AD-Synchronisierungskonto
Sie können dem Synchronisierungsdienst das Kennwort des Azure AD-Dienstkontos nicht direkt bereitstellen. Stattdessen müssen Sie das Azure AD-Dienstkonto über das Cmdlet **Add-ADSyncAADServiceAccount** erneut initialisieren. Das Cmdlet setzt das Kontokennwort zurück und stellt es dem Synchronisierungsdienst zur Verfügung:

1. Starten Sie eine neue PowerShell-Sitzung auf dem Azure AD Connect-Server.
2. Führen Sie das Cmdlet `Add-ADSyncAADServiceAccount` aus.
3. Geben Sie im Popupdialogfeld die Azure AD-Anmeldeinformationen des globalen Administrators für Ihren Azure AD-Mandanten an.
![Hilfsprogramm für den Verschlüsselungsschlüssel der Azure AD Connect-Synchronisierung](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Wenn der Vorgang erfolgreich ist, wird Ihnen die PowerShell-Eingabeaufforderung angezeigt.

#### <a name="start-the-synchronization-service"></a>Starten des Synchronisierungsdiensts
Der Synchronisierungsdienst besitzt jetzt Zugriff auf den Verschlüsselungsschlüssel und alle erforderlichen Kennwörter. Daher können Sie den Dienst im Windows-Dienststeuerungs-Manager neu starten:


1. Wechseln Sie zum Windows-Dienststeuerungs-Manager („START“ → „Dienste“).
2. Wählen Sie **Microsoft Azure AD-Synchronisierung**, und klicken Sie auf „Neu starten“.

## <a name="next-steps"></a>Nächste Schritte
**Übersichtsthemen**

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)

* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
