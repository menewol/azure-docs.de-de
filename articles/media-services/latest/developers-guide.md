---
title: Azure Media Services v3 SDKs – Azure
description: Dieser Artikel enthält eine Übersicht über die ersten Entwicklungsschritte mit der Media Services v3-API und mit SDKs.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9fb4d1561a661387f759aada9e776d43a95aa5c7
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564508"
---
# <a name="develop-against-media-services-v3-api-using-sdks"></a>Entwickeln mit der Media Services v3-API und SDKs

Als Entwickler können Sie die [REST-API](https://aka.ms/ams-v3-rest-ref) von Media Services oder Clientbibliotheken verwenden, die es Ihnen ermöglichen, mit der REST-API auf einfache Weise zu interagieren, um benutzerdefinierte Medienworkflows zu erstellen, zu verwalten und zu pflegen. Die [Media Services v3](https://aka.ms/ams-v3-rest-sdk)-API basiert auf der OpenAPI-Spezifikation (ehemals Swagger).

> [!NOTE]
> Für die Azure Media Services v3 SDKs wird keine Threadsicherheit garantiert. Wenn Sie eine Multithreadanwendung entwickeln, sollten Sie zum Schutz des Clients Ihre eigene Threadsynchronisierungslogik hinzufügen oder für jeden Thread ein neues AzureMediaServicesClient-Objekt verwenden. Achten Sie außerdem auf Multithreadingprobleme, die durch optionale Objekte entstehen können, die von Ihrem Code für den Client bereitgestellt werden (beispielsweise eine HttpClient-Instanz in .NET).

Dieses Thema enthält Links zu den SDKs, Tools und Schrittanleitungen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Entwicklung mit Media Services benötigen Sie Folgendes:

- Ein aktives Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
- [Kenntnisse grundlegender Konzepte](concepts-overview.md)
- Lesen Sie [Entwickeln mit Media Services v3-APIs](media-services-apis-overview.md).
- [CLI-Beispiel: Erstellen eines Azure Media Services-Kontos](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Einstieg in die Entwicklung mit SDKs

### <a name="net"></a>.NET

Verwenden Sie das [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk), um eine [Verbindung mit Media Services](configure-connect-dotnet-howto.md) herzustellen.

Machen Sie sich mit der [.NET-Referenzdokumentation](https://aka.ms/ams-v3-dotnet-ref) für Media Services vertraut.

### <a name="java"></a>Java

Verwenden Sie das [Java SDK](https://aka.ms/ams-v3-java-sdk), um eine [Verbindung mit Media Services](configure-connect-java-howto.md) herzustellen.

Machen Sie sich mit der [Java-Referenzdokumentation](https://aka.ms/ams-v3-java-ref) zu Media Services vertraut.

### <a name="nodejs"></a>Node.js

Verwenden Sie das [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk), um eine [Verbindung mit Media Services](configure-connect-nodejs-howto.md) herzustellen.

Machen Sie sich mit der [Node.js-Referenzdokumentation](https://aka.ms/ams-v3-nodejs-ref) für Media Services sowie mit [Beispielen](https://github.com/Azure-Samples/media-services-v3-node-tutorials) vertraut, die die Verwendung der Media Services-API mit Node.js veranschaulichen.

### <a name="python"></a>Python

Verwenden Sie das [Python SDK](https://aka.ms/ams-v3-python-sdk).

Machen Sie sich mit der [Python-Referenzdokumentation](https://aka.ms/ams-v3-python-ref) zu Media Services vertraut.

### <a name="go"></a>Go

Verwenden Sie das [Go SDK](https://aka.ms/ams-v3-go-sdk).

Machen Sie sich mit der [Go-Referenzdokumentation](https://aka.ms/ams-v3-go-ref) zu Media Services vertraut.

### <a name="ruby"></a>Ruby

Verwenden Sie das [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) ist ein Tool für Windows-Kunden, die sich über Media Services informieren möchten. Bei AMSE handelt es sich um eine WinForms-/C#-Anwendung zum Hochladen, Herunterladen, Codieren und Streamen von VoD- und Liveinhalten mit Media Services. Das AMSE-Tool richtet sich an Kunden, die Media Services ohne Programmieraufwand testen möchten. Der AMSE-Code wird als Ressource für Kunden bereitgestellt, die mit Media Services entwickeln möchten.

AMSE ist ein Open-Source-Projekt mit communitybasiertem Support. (Probleme können hier gemeldet werden: https://github.com/Azure/Azure-Media-Services-Explorer/issues) Für dieses Projekt wird der [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Verhaltenskodex für Microsoft Open Source) beachtet. Weitere Informationen finden Sie in den [häufig gestellten Fragen zum Verhaltenskodex](https://opensource.microsoft.com/codeofconduct/faq/). Sie können sich auch an opencode@microsoft.com wenden, wenn Sie weitere Fragen oder Anmerkungen haben.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](media-services-overview.md)
