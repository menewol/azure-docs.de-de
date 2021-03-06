---
title: Includedatei
description: Includedatei
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5eaee4f932c4e42f6fed3d839314346b3a93f360
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799527"
---
## <a name="register-your-application"></a>Anwendung registrieren

Sie können Ihre Anwendung auf zwei Arten registrieren.

### <a name="option-1-express-mode"></a>Option 1: Expressmodi

Gehen Sie zur schnellen Registrierung Ihrer Anwendung wie folgt vor:
1. Navigieren Sie zur [Anwendungsregistrierung im Azure-Portal](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit nur einem Klick herunterzuladen und automatisch zu konfigurieren.

### <a name="option-2-advanced-mode"></a>Option 2: Erweiterter Modus

Wenn Sie Ihre Anwendung registrieren und die Anwendungsregistrierungsinformationen Ihrer Projektmappe hinzufügen möchten, führen Sie folgende Schritte aus:
1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
1. Navigieren Sie zur Seite [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) von Microsoft Identity Platform für Entwickler.
1. Wählen Sie **Neue Registrierung** aus.
   - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird (beispielsweise `Win-App-calling-MsGraph`).
   - Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten (z. B. Skype, Xbox, Outlook.com)** aus.
   - Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Wählen Sie in der Liste mit den Seiten für die App die Option **Authentifizierung** aus.
1. Navigieren Sie im Abschnitt **Umleitungs-URIs** zum Abschnitt **Vorgeschlagene Umleitungs-URIs für öffentliche Clients (Mobilgerät, Desktop)**, und wählen Sie **"urn:ietf:wg:oauth:2.0:oob** aus.
1. Wählen Sie **Speichern** aus.
1. Öffnen Sie in Visual Studio die Datei *App.xaml.cs*, und ersetzen Sie `Enter_the_Application_Id_here` durch die Anwendungs-ID, die Sie soeben registriert und kopiert haben.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
