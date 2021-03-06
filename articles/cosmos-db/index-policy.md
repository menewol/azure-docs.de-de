---
title: Indizierungsrichtlinien für Azure Cosmos DB
description: Erhalten Sie Informationen zur Funktionsweise der Indizierung in Azure Cosmos DB. In diesem Artikel wird das Konfigurieren und Ändern der Indizierungsrichtlinie zur automatischen Indizierung und zur Steigerung der Leistung erläutert.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 6998db1679e67f8ac4bf7c81ea9373c66a9618ee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278563"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Indizierungsrichtlinie in Azure Cosmos DB

Sie können die standardmäßige Indizierungsrichtlinie für einen Azure Cosmos-Container überschreiben, indem Sie die folgenden Parameter konfigurieren:

* **Ein- oder Ausschließen von Elementen und Pfaden im Index:** Sie können bestimmte Elemente aus dem Index ausschließen oder im Index einschließen, wenn Sie die Elemente in einem Container einfügen oder ersetzen. Sie können auch bestimmte Pfade/Eigenschaften, die über Container hinweg indiziert werden sollen, ein- oder ausschließen. Pfade können Platzhaltermuster enthalten, z.B. *.

* **Konfigurieren von Indextypen:** Zusätzlich zu den bereichsindizierten Pfaden können Sie weitere Indextypen hinzufügen, z. B. den räumlichen Index.

* **Konfigurieren von Indizierungsmodi:** Durch die Verwendung der Indizierungsrichtlinie für einen Container können Sie verschiedene Indizierungsmodi wie *Konsistent* oder *Kein* konfigurieren.

## <a name="indexing-modes"></a>Indizierungsmodi

Azure Cosmos DB unterstützt zwei Indizierungsmodi, die Sie in einem Azure Cosmos-Container über eine Indizierungsrichtlinie konfigurieren können:

* **Konsistent:** Wenn die Richtlinie eines Azure Cosmos-Containers auf *Konsistent* festgelegt ist, gilt für die Abfragen für einen bestimmten Container die gleiche Konsistenzebene wie die für Punktlesevorgänge angegebene Ebene (z. B. „stark“, „begrenzte Veraltung“, „Sitzung“ oder „letztlich“). 

  Der Index wird synchron aktualisiert, wenn Sie die Elemente aktualisieren. Beispielsweise führen Einfüge-, Ersetzungs-, Aktualisierungs- und Löschvorgänge an einem Element zum Aktualisieren des Index. Die konsistente Indizierung unterstützt konsistente Abfragen auf Kosten einer möglichen Verringerung des Schreibdurchsatzes. Die Reduzierung des Schreibdurchsatzes hängt von den „Pfaden im Index“ und der „Konsistenzebene“ ab. Der konsistente Indizierungsmodus dient dazu, den Index mit allen Updates aktuell zu halten und Abfragen direkt zu verarbeiten.

* **Keine:** Einem Container mit dem Indizierungsmodus „Kein“ ist kein Index zugeordnet. Diese Option wird häufig verwendet, wenn Azure Cosmos DB als Schlüssel-Wert-Speicher genutzt wird und auf Elemente nur nach ihrer ID-Eigenschaft zugegriffen wird.

  > [!NOTE]
  > Das Konfigurieren des Indizierungsmodus als *Keine* hat die Nebenwirkung, dass alle vorhandenen Indizes gelöscht werden. Verwenden Sie diese Option, wenn Ihre Zugriffsmuster nur „ID“ oder „Self-Link“ erfordern.

Konsistenzebenen für Abfragen werden ähnlich wie reguläre Lesevorgänge verwaltet. Azure Cosmos DB gibt einen Fehler zurück, wenn Sie den Container mit dem Indizierungsmodus *Keine* abfragen. Sie können Abfragen als Scans über den expliziten Header **x-ms-documentdb-enable-scan** in der REST-API oder über die Anforderungsoption **EnableScanInQuery** mithilfe des .NET SDK ausführen. Einige Abfragefunktionen, wie ORDER BY, werden derzeit von **EnableScanInQuery** nicht unterstützt, da sie einen entsprechenden Index erfordern.

## <a name="modifying-the-indexing-policy"></a>Ändern der Indizierungsrichtlinie

In Azure Cosmos DB können Sie die Indizierungsrichtlinie eines Containers jederzeit aktualisieren. Eine Änderung der Indizierungsrichtlinie im Azure Cosmos-Container kann zu einer Änderung der Form des Index führen. Diese Änderung wirkt sich auf die indizierbaren Pfade, deren Genauigkeit und auf das Konsistenzmodell des Index aus. Eine Änderung der Indizierungsrichtlinie erfordert effektiv eine Transformation des alten Index in einen neuen Index.

### <a name="index-transformations"></a>Indextransformationen

Alle Indextransformationen erfolgen online. Die nach der alten Richtlinie indizierten Elemente werden effizient anhand der neuen Richtlinie transformiert, ohne die Schreibverfügbarkeit oder den Durchsatz, der für den Container bereitgestellt wird, zu beeinträchtigen. Die Indextransformation hat keine Auswirkungen auf die Konsistenz der Lese- und Schreibvorgänge, die mithilfe der REST-API, SDKs oder gespeicherter Prozeduren und Trigger erfolgen.

Das Ändern der Indizierungsrichtlinie ist ein asynchroner Vorgang, und die Zeit, die für die Durchführung des Vorgangs benötigt wird, hängt von der Anzahl von Elementen, dem bereitgestellten Durchsatz und der Größe der Elemente ab. Während die Neuindizierung durchgeführt wird, gibt Ihre Abfrage ggf. nicht alle übereinstimmenden Ergebnisse zurück, wenn die Abfrage den Index verwendet, der gerade geändert wird. Bei Abfragen werden dann keine Fehler bzw. Ausfälle zurückgegeben. Während der Neuindizierung werden Abfragen unabhängig von der Indizierungsmoduskonfiguration letztendlich konsistent durchgeführt. Nachdem die Indextransformation abgeschlossen ist, werden weiterhin konsistente Ergebnisse angezeigt. Dies gilt für Abfragen, die von den Schnittstellen ausgegeben werden – z.B. REST-API, SDKs oder gespeicherte Prozeduren und Trigger. Die Indextransformation wird asynchron im Hintergrund auf den Replikaten mithilfe der freien Ressourcen ausgeführt, die für ein bestimmtes Replikat verfügbar sind.

Alle Indextransformationen werden direkt vorgenommen. Azure Cosmos DB behält nicht zwei Kopien des Index bei. Dies bedeutet, dass beim Ausführen von Indextransformationen in Ihren Containern kein zusätzlicher Speicherplatz erforderlich ist oder belegt wird.

Wenn Sie die Indizierungsrichtlinie ändern, werden die Änderungen aus dem alten Index in erster Linie auf der Grundlage der Konfigurationen des Indizierungsmodus in den neuen Index übernommen. Konfigurationen des Indizierungsmodus spielen eine große Rolle im Vergleich zu anderen Eigenschaften wie eingeschlossenen/ausgeschlossenen Pfaden, Indexarten und Genauigkeit.

Wenn sowohl die alten als auch die neuen Richtlinien **konsistente** Indizierung verwenden, führt Azure Cosmos DB eine Onlineindextransformation durch. Während der Ausführung der Transformation können Sie keine weitere Änderung an der Indizierungsrichtlinie vornehmen, die den Indizierungsmodus „Konsistent“ aufweist. Wenn Sie zum Indizierungsmodus „Keine“ wechseln, wird der Index sofort gelöscht. Der Wechseln zu „Keine“ ist nützlich, wenn Sie eine laufende Transformation abbrechen und mit einer anderen Indizierungsrichtlinie neu beginnen möchten.

## <a name="modifying-the-indexing-policy---examples"></a>Ändern der Indizierungsrichtlinie – Beispiele

Im Folgenden finden Sie die häufigsten Anwendungsfälle, in denen Sie eine Indizierungsrichtlinie aktualisieren:

* Wenn Sie im Normalbetrieb konsistente Ergebnisse erzielen möchten, aber bei Massendatenimporten den Indizierungsmodus **Keine** nutzen möchten.

* Wenn Sie mit der Verwendung von Indizierungsfunktionen für Ihre aktuellen Azure Cosmos-Container beginnen möchten. Sie können z. B. räumliche Abfragen verwenden, die die Indexart „Spatial“ erfordern, oder ORDER BY-/Zeichenfolgenbereich-Abfragen, die die Indexart „Range“ für Zeichenfolgen erfordern.

* Wenn Sie die zu indizierenden Eigenschaften manuell auswählen und im Laufe der Zeit ändern möchten, um sie an Ihre Workloads anzupassen.

* Wenn Sie die Indizierungsgenauigkeit optimieren möchten, um die Abfrageleistung zu verbessern oder den verbrauchten Speicherplatz zu reduzieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Indizierung erhalten Sie in den folgenden Artikeln:

* [Übersicht über die Indizierung](index-overview.md)
* [Indextypen](index-types.md)
* [Indexpfade](index-paths.md)
* [Verwalten von Indizierungsrichtlinien](how-to-manage-indexing-policy.md)
