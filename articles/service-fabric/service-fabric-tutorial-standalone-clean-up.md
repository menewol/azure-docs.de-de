---
title: 'Tutorial: Bereinigen des eigenständigen Service Fabric-Clusters – Azure Service Fabric | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie Ihren eigenständigen Cluster bereinigen.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 28cc2bf4794620641fb6af46bd4017d74f87e955
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666992"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: Bereinigen Ihres eigenständigen Clusters

Mit eigenständigen Service Fabric-Clustern können Sie Ihre eigene Umgebung wählen und einen Cluster im Rahmen des Service Fabric-Konzepts „Jedes Betriebssystem, jede Cloud“ erstellen. In dieser Tutorialreihe erstellen Sie einen eigenständigen, in AWS gehosteten Cluster und installieren darin eine Anwendung.

Dieses Tutorial ist der vierte Teil einer Serie. In diesem Teil des Tutorials erfahren Sie, wie Sie die AWS-Ressourcen bereinigen, die Sie zum Hosten Ihres Service Fabric-Clusters erstellt haben.

Im vierten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereinigen des Service Fabric-Clusters
> * Bereinigen Ihrer AWS-Ressourcen

## <a name="clean-up-service-fabric-cluster"></a>Bereinigen des Service Fabric-Clusters

* Stellen Sie eine RDP-Verbindung mit der EC2-Instanz her, die Sie zum Installieren von Service Fabric verwendet haben.
* Öffnen Sie PowerShell.
* Wechseln Sie zum Verzeichnis des extrahierten Ordners aus dem zweiten Tutorial.
* Führen Sie den folgenden Befehl aus, um den Service Fabric-Cluster zu entfernen:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* Drücken Sie `Y`, wenn Sie dazu aufgefordert werden. Wenn der Vorgang erfolgreich war, erhalten Sie eine Ausgabe wie die folgende (mit Ihren eigenen IP-Adressen):

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>Bereinigen der AWS-Ressourcen

* Melden Sie sich bei Ihrem AWS-Konto an.
* Navigieren Sie zur EC2-Konsole.
* Wählen Sie die drei Knoten aus, die Sie im ersten Teil des Tutorials erstellt haben.
* Klicken Sie auf **Aktionen** > **Instanzstatus** > **Beenden**.

## <a name="next-steps"></a>Nächste Schritte

Im vierten Teil der Reihe haben Sie gelernt, wie Sie die Ressourcen bereinigen, die in den vorherigen Schritten erstellt wurden.

> [!div class="checklist"]
> * Bereinigen von Ressourcen

> [!div class="nextstepaction"]
> [Zum Anfang](service-fabric-tutorial-standalone-create-infrastructure.md)