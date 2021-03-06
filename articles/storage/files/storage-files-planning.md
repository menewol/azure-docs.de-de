---
title: Planung für eine Azure Files-Bereitstellung | Microsoft-Dokumentation
description: Erfahren Sie, was Sie beim Planen einer Azure Files-Bereitstellung berücksichtigen müssen.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 609e774c36ab685d017f311a74c8680dbb9750c9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283017"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planung für eine Azure Files-Bereitstellung

[Azure Files](storage-files-introduction.md) bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das Standardprotokoll SMB zugegriffen werden kann. Da Azure Files vollständig verwaltet ist, ist die Bereitstellung in Produktionsszenarien wesentlich einfacher als das Bereitstellen und Verwalten eines Dateiservers oder NAS-Geräts. In diesem Artikel werden die Aspekte behandelt, die beim Bereitstellen einer Azure-Dateifreigabe in der Produktionsumgebung Ihrer Organisation zu berücksichtigen sind.

## <a name="management-concepts"></a>Verwaltungskonzepte

 Das folgende Diagramm veranschaulicht die Verwaltungskonstrukte von Azure Files:

![Dateistruktur](./media/storage-files-introduction/files-concepts.png)

* **Storage Account** (Speicherkonto): Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Ausführliche Informationen zur Kapazität von Speicherkonten finden Sie unter [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Skalierbarkeits- und Leistungsziele für Azure Storage).

* **Freigabe:** Eine Datenspeicherfreigabe ist eine SMB-Dateifreigabe in Azure. Alle Verzeichnisse und Dateien müssen in der übergeordneten Freigabe erstellt werden. Ein Konto kann eine unbegrenzte Anzahl von Freigaben enthalten, und eine Freigabe kann eine unbegrenzte Anzahl von Dateien speichern, bis die Kapazitätsgrenze von 5 TiB für die Dateifreigabe erreicht ist.

* **Verzeichnis:** Eine optionale Hierarchie von Verzeichnissen.

* **Datei:** Eine Datei in der Freigabe. Die Datei kann bis zu 1 TiB groß sein.

* **URL-Format:** Für Anforderung an eine Azure-Dateifreigabe mithilfe des REST-Protokolls „File“ sind Dateien mit dem folgenden URL-Format adressierbar:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Datenzugriffsmethode

Azure Files bietet zwei integrierte, praktische Datenzugriffsmethoden, die Sie für den Zugriff auf Ihre Daten getrennt oder gemeinsam verwenden können:

1. **Direkter Cloudzugriff:** Azure-Dateifreigaben können unter [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) und/oder [Linux](storage-how-to-use-files-linux.md) mithilfe des Standardprotokolls Server Message Block (SMB) oder über die REST-API „File“ eingebunden werden. Bei SMB erfolgen Lese- und Schreibvorgänge in Dateien auf der Freigabe direkt auf der Dateifreigabe in Azure. Zur Bereitstellung durch eine VM in Azure muss der SMB-Client des Betriebssystems mindestens SMB 2.1 unterstützen. Für eine lokale Bereitstellung, z.B. auf der Arbeitsstation eines Benutzers, muss der von der Arbeitsstation unterstützte SMB-Client mindestens SMB 3.0 (mit Verschlüsselung) unterstützen. Zusätzlich zu SMB können neue Anwendungen oder Dienste über die REST-API „File“ direkt auf die Dateifreigabe zugreifen. Diese stellt eine einfache und skalierbare Anwendungsprogrammierschnittstelle für die Softwareentwicklung bereit.
2. **Azure-Dateisynchronisierung:** Mit der Azure-Dateisynchronisierung können die Freigaben auf Windows Server-Computern lokal oder in Azure repliziert werden. Ihre Benutzer greifen auf die Dateifreigabe über den Windows-Server zu, z.B. über eine SMB- oder NFS-Freigabe. Dies ist nützlich für Szenarien, in denen Daten weit entfernt von einem Azure-Rechenzentrum abgerufen und geändert werden, z.B. in einer Zweigstelle. Daten können zwischen mehreren Windows Server-Endpunkten repliziert werden, z.B. zwischen mehreren Zweigstellen. Schließlich können Daten in Azure Files mehrstufig gespeichert werden, z.B. so, dass über den Server weiter auf alle Daten zugegriffen werden kann, der Server aber nicht über eine vollständige Kopie der Daten verfügt. Vielmehr werden die Daten beim Öffnen durch den Benutzer nahtlos abgerufen.

Die folgende Tabelle verdeutlicht, wie Ihre Benutzer und Anwendungen auf Ihre Azure-Dateifreigabe zugreifen können:

| | Direkter Cloudzugriff | Azure-Dateisynchronisierung |
|------------------------|------------|-----------------|
| Welche Protokolle müssen Sie verwenden? | Azure Files unterstützt SMB 2.1, SMB 3.0 und die REST-API „File“. | Auf die Azure-Dateifreigabe kann über alle unter Windows Server unterstützten Protokolle (SMB, NFS, FTPS usw.) zugegriffen werden. |  
| Wo wird Ihre Workload ausgeführt? | **In Azure:** Azure Files bietet einen direkten Zugriff auf Ihre Daten. | **Lokal mit langsamem Netzwerk:** Windows-, Linux- und macOS-Clients können eine lokale Windows-Dateifreigabe als schnellen Cache Ihrer Azure-Dateifreigabe einbinden. |
| Welche Art von Zugriffssteuerungslisten benötigen Sie? | Freigabe- und Dateiebene. | Freigabe-, Datei- und Benutzerebene. |

## <a name="data-security"></a>Datensicherheit

Azure Files bietet mehrere integrierte Optionen zum Gewährleisten der Datensicherheit:

* Unterstützung für die Verschlüsselung in beiden drahtgebundenen Protokollen: SMB 3.0-Verschlüsselung und REST-API „File“ über HTTPS. Standardmäßig gilt: 
    * Clients, die die SMB 3.0-Verschlüsselung unterstützen, senden und empfangen Daten über einen verschlüsselten Kanal.
    * Clients, die SMB 3.0 mit Verschlüsselung nicht unterstützen, können innerhalb des Rechenzentrums über SMB 2.1 oder SMB 3.0 ohne Verschlüsselung kommunizieren. SMB-Clients dürfen nicht unverschlüsselt zwischen Rechenzentren über SMB 2.1 oder SMB 3.0 kommunizieren.
    * Clients können über die REST-API „File“ mit HTTP oder HTTPS kommunizieren.
* Verschlüsselung ruhender Daten ([Azure-Speicherdienstverschlüsselung](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): Die Speicherdienstverschlüsselung (SSE) ist für alle Speicherkonten aktiviert. Ruhende Daten werden mit vollständig verwalteten Schlüsseln verschlüsselt. Durch Verschlüsselung ruhender Daten erhöhen sich weder die Speicherkosten, noch wird die Leistung verringert. 
* Optionale Anforderung der Verschlüsselung von Daten während der Übertragung: Bei Auswahl dieser Option lehnt Azure Files den Zugriff auf die Daten über unverschlüsselte Kanäle ab. Konkret sind bei verschlüsselten Verbindungen nur HTTPS und SMB 3.0 zugelassen.

    > [!Important]  
    > Die Notwendigkeit einer sicheren Datenübertragung führt dazu, dass ältere SMB-Clients, die nicht in der Lage sind, mit SMB 3.0 mit Verschlüsselung zu kommunizieren, nicht funktionieren. Weitere Informationen finden Sie unter [Einbinden unter Windows](storage-how-to-use-files-windows.md), [Einbinden unter Linux](storage-how-to-use-files-linux.md) und [Einbinden unter macOS](storage-how-to-use-files-mac.md).

Für maximale Sicherheit empfehlen wir dringend, stets sowohl die Verschlüsselung ruhender Daten als auch die Verschlüsselung von Daten während der Übertragung zu aktivieren, wenn Sie moderne Clients für den Zugriff auf Ihre Daten verwenden. Wenn Sie z. B. eine Freigabe auf einer VM mit Windows Server 2008 R2 bereitstellen müssen, die nur SMB 2.1 unterstützt, müssen Sie unverschlüsselten Datenverkehr zu Ihrem Speicherkonto zulassen, da SMB 2.1 keine Verschlüsselung unterstützt.

Wenn Sie über die Azure-Dateisynchronisierung auf Ihre Azure-Dateifreigabe zugreifen, verwenden wir stets HTTPS und SMB 3.0 mit Verschlüsselung, um Ihre Daten mit Ihren Windows-Servern zu synchronisieren. Dies gilt unabhängig davon, ob Sie eine Verschlüsselung ruhender Daten benötigen.

## <a name="file-share-performance-tiers"></a>Leistungsstufen von Dateifreigaben

Azure Files bietet zwei Leistungsstufen: Standard und Premium.

* **Standard-Dateifreigaben** werden durch rotierende Festplattenlaufwerke (HDDs) gestützt, die eine zuverlässige Leistung für E/A-Workloads bieten, die weniger anfällig für Leistungsschwankungen sind, z.B. Dateifreigaben für allgemeine Zwecke und Dev/Test-Umgebungen. Standard-Dateifreigaben sind nur in einem nutzungsbasierten Abrechnungsmodell verfügbar.
* **Premium-Dateifreigaben (Vorschauversion)** werden durch Solid-State-Laufwerke (SSDs) gestützt, die konsistent hohe Leistung und niedrige Latenz im einstelligen Millisekundenbereich für die meisten E/A-Vorgänge für Workloads mit besonders umfassenden E/A bieten. Dadurch sind sie für eine Vielzahl von Workloads wie Datenbanken, Websitehosting, Entwicklungsumgebungen usw. geeignet. Premium-Dateifreigaben sind nur in einem Abrechnungsmodell nach Bereitstellung verfügbar. Premium-Dateifreigaben verwenden ein von Standarddateifreigaben separates Bereitstellungsmodell. Wenn Sie erfahren möchten, wie Sie eine Premium-Dateifreigabe erstellen, lesen Sie unseren Artikel zu diesem Thema: [Erstellen einer Azure- Premium-Dateifreigabe](storage-how-to-create-premium-fileshare.md).

> [!IMPORTANT]
> Premium-Dateifreigaben befinden sich noch in der Vorschau, sind nur mit LRS und nur in einem Teil der Regionen verfügbar, wobei Azure Backup-Unterstützung in ausgewählten Regionen verfügbar ist:

|Verfügbare Region  |Azure Backup-Unterstützung  |
|---------|---------|
|USA (Ost 2)      | Ja|
|USA (Ost)       | Ja|
|USA (Westen)       | Nein  |
|USA, Westen 2      | Nein  |
|USA (Mitte)    | Nein  |
|Nordeuropa  | Nein  |
|Europa, Westen   | Ja|
|Südostasien       | Ja|
|Japan, Osten    | Nein  |
|Korea, Mitte | Nein  |
|Australien (Osten)| Nein  |

### <a name="provisioned-shares"></a>Bereitgestellte Freigaben

Premium-Dateifreigaben (Vorschau) werden basierend auf einem festen Verhältnis aus GiB/IOPS/Durchsatz bereitgestellt. Für jedes bereitgestellte GiB erhält die Freigabe 1 IOPS und einen Durchsatz von 0,1 MiB/s bis zum maximalen Grenzwert pro Freigabe. Die kleinste zulässige Bereitstellung beträgt 100 GiB mit den minimalen Werten für IOPS/Durchsatz. Die Größe der Dateifreigabe kann jederzeit erhöht oder verringert werden, sie kann jedoch nur einmal alle 24 Stunden seit der letzten Erhöhung verringert werden.

Auf einer Best-Effort-Basis können alle Freigaben für 60 Minuten oder länger (je nach Größe der Freigabe) auf bis zu 3 IOPS pro GiB an bereitgestelltem Speicher erhöht werden (Burst). Neue Freigaben beginnen mit dem vollständigen Burstguthaben, basierend auf der bereitgestellten Kapazität.

Alle Freigaben können Burstübertragungen bis zu mindestens 100IOPS und einem Zieldurchsatz von 100MiB/s durchführen. Freigaben müssen in Schritten von 1GiB bereitgestellt werden. Die minimale Größe beträgt 100GiB, die nächste Größe ist 101GIB usw.

> [!TIP]
> IOPS-Grundwert = 100 + 1 * bereitgestellte GiB. (Bis zu 100.000 IOPS).
>
> Burstgrenzwert = 3 * IOPS-Grundwert. (Bis zu 100.000 IOPS).
>
> Ausgangsrate = 60MiB/s + 0,06 bereitgestellte GiB (bis zu 6GiB/s)
>
> Eingangsrate = 40MiB/s + 0,04 bereitgestellte GiB (bis zu 4GiB/s)

Die Größe der Dateifreigabe kann jederzeit erhöht oder verringert werden, sie kann jedoch nur einmal alle 24 Stunden seit der letzten Erhöhung verringert werden. Änderungen von IOPS/Durchsatz werden innerhalb von 24 Stunden nach der Größenänderung wirksam.

Die folgende Tabelle zeigt einige Beispiele dieser Formeln für die bereitgestellten Freigabengrößen:

(durch ein * gekennzeichnete Größen sind in der begrenzten Public Preview)

|Kapazität (GiB) | IOPS-Grundwert | Burstgrenzwert | Ausgehend (MiB/s) | Eingehend (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Bis zu 300     | 66   | 44   |
|500         | 500     | Bis zu 1.500   | 90   | 60   |
|1024       | 1024   | Bis zu 3.072   | 122   | 81   |
|5.120       | 5.120   | Bis zu 15.360  | 368   | 245   |
|10.240 *     | 10.240  | Bis zu 30.720  | 675 | 450   |
|33.792 *     | 33.792  | Bis zu 100.000 | 2.088 | 1.392   |
|51.200 *     | 51.200  | Bis zu 100.000 | 3.132 | 2.088   |
|100.000 *    | 100.000 | Bis zu 100.000 | 6.204 | 4.136   |

Aktuell sind Freigabegrößen bis zu 5TiB in der Public Preview, während Größen bis zu 102TiB in begrenzter Public Preview sind. Um Zugriff auf die begrenzte Public Preview anzufordern, füllen Sie [diesen Fragebogen](https://aka.ms/azurefilesatscalesurvey) aus.

### <a name="bursting"></a>Bursting

Premium-Dateifreigaben können ihren IOPS-Wert bis zu Faktor drei erhöhen. Bursting wird automatisiert und funktioniert auf Basis eines Guthabensystems. Die Burstübertragung funktioniert auf Best-Effort-Basis, und der Burstgrenzwert ist keine Garantie; bei Dateifreigaben ist eine Burstübertragung *bis zum* Grenzwert möglich.

Guthaben sammeln sich in einem Burstbucket an, wenn Datenverkehr für Ihre Dateifreigaben unterhalb des IOPS-Grundwerts liegt. Beispielsweise hat eine 100GiB-Freigabe 100 IOPS-Grundwerte. Wenn der eigentliche Datenverkehr auf der Freigabe 40IOPS für ein bestimmtes 1-Sekunden-Intervall betrug, werden die 60 nicht verwendeten IOPS einem Burstbucket gutgeschrieben. Diese Guthaben werden dann später verwendet, wenn Vorgänge die IOPS-Grundwerte überschreiten.

> [!TIP]
> Größe des Burstgrenzwertbuckets = IOPS-Grundwert * 2 * 3600.

Wenn eine Freigabe den IOPS-Grundwert überschreitet und Guthaben in einem Burstbucket hat, führt sie Burstübertragungen durch. Freigaben können solange Burstübertragungen durchführen, wie Guthaben übrig sind, aber Freigaben, die kleiner sind als 50TiB, bleiben nur bis zu einer Stunde auf dem Burstgrenzwert. Freigaben, die größer sind als 50TiB, können dieses einstündige Limit technisch überschreiten, bis zu zwei Stunden, aber dies basiert auf der Anzahl der gesammelten Burstguthaben. Jede EA über dem IOPS-Grundwert verbraucht ein Guthaben, und wenn alle Guthaben verbraucht sind, kehrt die Freigabe zum IOPS-Grundwert zurück.

Freigabeguthaben können drei Zustände aufweisen:

- Anwachsend, wenn die Dateifreigabe weniger als den IOPS-Grundwert verwendet.
- Sinkend, wenn die Dateifreigabe Burstübertragungen durchführt.
- Bei null (0) verbleibend, wenn entweder keine Guthaben vorhanden sind oder der IOPS-Grundwert verwendet wird.

Neue Dateifreigaben beginnen mit der vollen Anzahl von Guthaben im Burstbucket.

## <a name="file-share-redundancy"></a>Dateifreigaberedundanz

Azure Files-Standardfreigaben unterstützen drei Optionen für Datenredundanz: lokal redundanter Speicher (LRS), zonenredundanter Speicher (ZRS) und georedundanter Speicher (GRS).

Azure Files-Premiumfreigaben unterstützen nur lokal redundanten Speicher (LRS).

In den folgenden Abschnitten werden die Unterschiede zwischen den verschiedenen Redundanzoptionen erläutert:

### <a name="locally-redundant-storage"></a>Lokal redundanter Speicher

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Zonenredundanter Speicher

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Georedundanter Speicher

> [!Warning]  
> Wenn Sie Ihre Azure-Dateifreigabe als Cloudendpunkt in einem GRS-Speicherkonto verwenden, sollten Sie kein Failover des Speicherkontos einleiten. Dies würde das Funktionieren der Synchronisierung beenden und könnte außerdem bei neu einbezogenen Dateien zu unerwartetem Datenverlust führen. Im Fall des Ausfalls einer Azure-Region löst Microsoft das Failover des Speicherkontos auf eine Weise aus, die mit der Azure-Dateisynchronisierung kompatibel ist.

Georedundanter Speicher (GRS) ist so konzipiert, dass er eine Dauerhaftigkeit von mindestens 99,99999999999999999999 % (16 mal die 9) von Objekten über ein gegebenes Jahr bereitstellt, indem Ihre Daten in eine sekundäre Region repliziert werden, die Hunderte Kilometer von der primären Region entfernt ist. Wenn für Ihr Speicherkonto GRS aktiviert ist, sind Ihre Daten beständig gespeichert, selbst bei einem regionalen Komplettausfall oder einem Notfall, nach dem die primäre Region nicht mehr wiederhergestellt werden kann.

Wenn Sie sich für schreibgeschützten georedundanten Speicher (RA-GRS) entscheiden, sollten Sie wissen, dass Azure File schreibgeschützten georedundanten Speicher (RA-GRS) zu diesem Zeitpunkt in keiner Region unterstützt. Dateifreigaben im RA-GRS-Speicherkonto funktionieren wie in GRS-Konten und werden mit GRS-Preisen abgerechnet.

GRS repliziert Ihre Daten in ein anderes Rechenzentrum in einer sekundären Region, aber diese Daten sind nur schreibgeschützt verfügbar, wenn Microsoft ein Failover von der primären in die sekundäre Region initiiert.

Bei einem Speicherkonto mit aktiviertem GRS werden alle Daten zunächst mit dem lokal redundanten Speicher (LRS) repliziert. Ein Update wird zunächst an den primären Speicherort übertragen und mit LRS repliziert. Anschließend wird das Update asynchron mit GRS in die sekundäre Region repliziert. Wenn Daten in den sekundären Speicherort geschrieben werden, werden sie dort auch mit LRS repliziert.

Sowohl in der primären als auch in der sekundären Region werden Replikate über separate Fehlerdomänen und Upgradedomänen hinweg in einer Speicherskalierungseinheit verwaltet. Die Speicherskalierungseinheit ist die grundlegende Replikationseinheit im Datencenter. Die Replikation auf dieser Ebene wird von LRS bereitgestellt. Weitere Informationen finden Sie unter [Lokal redundanter Speicher (LRS): Kostengünstige Datenredundanz für Azure Storage](../common/storage-redundancy-lrs.md).

Beachten Sie diese Punkte, wenn Sie sich für eine Replikationsoption entscheiden:

* Zonenredundanter Speicher (ZRS) bietet eine hohe Verfügbarkeit mit synchroner Replikation und ist für einige Szenarien ggf. besser geeignet als GRS. Weitere Informationen zu ZRS finden Sie unter [ZRS](../common/storage-redundancy-zrs.md).
* Die asynchrone Replikation beinhaltet eine Verzögerung zwischen dem Zeitpunkt, zu dem diese Daten in der primären Region geschrieben werden, und dem Zeitpunkt, zu dem sie in der sekundären Region repliziert werden. Bei einem regionalen Notfall gehen Änderungen, die noch nicht in der sekundären Region repliziert wurden, möglicherweise verloren, wenn die Daten nicht in der primären Region wiederhergestellt werden können.
* Mit GRS ist das Replikat nicht für den Lese- oder Schreibzugriff verfügbar, sofern von Microsoft kein Failover in der sekundären Region initiiert wird. Im Fall eines Failovers erhalten Sie nach Abschluss des Failovers Lese- und Schreibzugriff auf diese Daten. Weitere Informationen finden Sie im [Leitfaden zur Notfallwiederherstellung](../common/storage-disaster-recovery-guidance.md).

## <a name="data-growth-pattern"></a>Muster des Datenwachstums

Die maximale Größe für eine Azure-Dateifreigabe ist derzeit 5TiB (100TiB für Premiumdateifreigabe in begrenzter Public Preview). Aufgrund dieser aktuellen Einschränkung müssen Sie das erwartete Wachstum berücksichtigen, wenn Sie eine Azure-Dateifreigabe bereitstellen.

Mithilfe der Azure-Dateisynchronisierung können mehrere Azure-Dateifreigaben mit einem einzelnen Windows-Dateiserver synchronisiert werden. Dadurch können Sie sicherstellen, dass ältere große Dateifreigaben, über die Sie möglicherweise lokal verfügen, in die Azure-Dateisynchronisierung übertragen werden können. Weitere Informationen finden Sie unter [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-files-planning.md).

## <a name="data-transfer-method"></a>Datenübertragungsmethode

Es gibt viele einfache Optionen, um Daten in einem Massenvorgang aus einer vorhandenen Dateifreigabe (z.B. einer lokalen Dateifreigabe) in Azure Files zu übertragen. Dazu zählen u.a.:

* **Azure-Dateisynchronisierung:** Als Teil einer ersten Synchronisierung zwischen einer Azure-Dateifreigabe (einem „Cloudendpunkt“) und einem Windows-Verzeichnisnamespace (einem „Serverendpunkt“) repliziert die Azure-Dateisynchronisierung alle Daten aus der vorhandenen Dateifreigabe in Azure Files.
* **[Azure Import/Export:](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** Mit dem Azure Import/Export-Dienst können Sie große Datenmengen auf sichere Weise in eine Azure-Dateifreigabe übertragen, indem Sie Festplattenlaufwerke an ein Azure-Rechenzentrum schicken. 
* **[Robocopy:](https://technet.microsoft.com/library/cc733145.aspx)** Robocopy ist ein bekanntes Kopiertool, das in Windows und Windows Server enthalten ist. Robocopy kann zum Übertragen von Daten in Azure Files verwendet werden, indem die Dateifreigabe lokal bereitgestellt wird. Anschließend wird der bereitgestellte Speicherort als Ziel des Robocopy-Befehls verwendet.
* **[AzCopy:](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)** AzCopy ist ein Befehlszeilenprogramm, das zum Kopieren von Daten in und aus Azure Files sowie Azure Blob Storage entwickelt wurde, wobei durch einfache Befehle eine optimale Leistung erzielt wird. AzCopy steht für Windows und Linux zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte
* [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
* [Bereitstellen von Azure Files](storage-files-deployment-guide.md)
* [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)
