---
title: Endpunkte für die Bing-Bildersuche-API
titleSuffix: Azure Cognitive Services
description: Eine Liste der verfügbaren Endpunkte für die Bing-Bildersuche-API.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: v-gedod
ms.openlocfilehash: fcae25ea0ef3f48beac6c221a11ca8a91443f7ce
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336139"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Endpunkte für die Bing-Bildersuche-API

Die **Bildersuche-API**  umfasst drei Endpunkte.  Endpunkt 1 gibt Bilder aus dem Internet basierend auf einer Abfrage zurück. Endpunkt 2 gibt [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) zurück.  Endpunkt 3 gibt beliebte Videos zurück.

## <a name="endpoints"></a>Endpunkte

Um mithilfe der Bing-API Bildergebnisse zu erhalten, senden Sie eine Anforderung an einen der folgenden Endpunkte. Verwenden Sie die Header und die URL-Parameter, um Spezifikationen genauer zu definieren.

**Endpunkt 1:** Gibt Bilder, die für die von `?q=""` definierte Suchabfrage des Benutzers relevant sind, zurück.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Endpunkt 2:** Gibt entweder mit `GET` oder `POST` Erkenntnisse zum Bild zurück.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Eine GET-Anforderung gibt Einblicke zu einem Bild zurück, z.B. Webseiten, die dieses Bild enthalten. Integrieren Sie den [InsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)-Parameter in eine `GET`-Anforderung.

Oder Sie können ein Binärbild in den Text einer `POST`-Anforderung einfügen und den [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested)-Parameter auf `RecognizedEntities` setzen. Dies gibt einen [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken)-Parameter zurück, der in einer nachfolgenden `GET`-Anforderung verwendet wird, die Informationen über Personen im Bild zurückgibt.  Setzen Sie `modules` auf `All`, um alle Einblicke zu erhalten, außer `RecognizedEntities` in den Ergebnissen von `POST`, ohne einen weiteren Aufruf mit `insightsToken` zu machen.


**Endpunkt 3:** Gibt Bilder zurück, die auf der Grundlage von Suchanfragen anderer beliebt sind. Die Bilder sind in verschiedene Kategorien eingeteilt, z.B. nach Personen oder Ereignissen.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Eine Liste der Märkte, die beliebte Bilder unterstützen, finden Sie unter [Beliebte Bilder](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Weitere Informationen zu Headern, Parametern, Marktcodes, Antwortobjekten, Fehlern usw. finden Sie in der Referenz [API für die Bing-Bildersuche v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
## <a name="response-json"></a>JSON-Antwort
Die Antwort auf eine Bildersuchanforderung enthält die Ergebnisse als JSON-Objekten. Beispiele für die Analyse der Ergebnisse finden Sie unter [Tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) und [Quellcode](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Nächste Schritte
Die **Bing**-APIs unterstützen Suchaktionen, die Ergebnisse gemäß ihrem Typ zurückgeben. Alle Suchendpunkte geben Ergebnisse als JSON-Antwortobjekte zurück.  Alle Endpunkte unterstützen Abfragen, die eine bestimmte Sprache und/oder einen bestimmten Ort nach Längengrad, Breitengrad und Suchradius zurückgeben.

Vollständige Informationen zu den Parametern, die von jedem Endpunkt unterstützt werden, finden Sie auf den Referenzseiten für jeden Typ.
Beispiele für grundlegende Anforderung mithilfe der Bildersuche-API finden Sie in den [Bildersuche-Schnellstarts](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
