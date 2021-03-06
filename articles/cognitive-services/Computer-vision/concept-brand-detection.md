---
title: Markenerkennung – Maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: Konzepte zur Erkennung von Marken/Logos mithilfe der Maschinelles Sehen-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: pafarley
ms.openlocfilehash: abeca204296bcb3933013f2b7434b8c558f62e50
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496586"
---
# <a name="brand-detection"></a>Markenerkennung

Markenerkennung ist ein spezieller Modus der [Objekterkennung](concept-object-detection.md), der eine Datenbank mit Tausenden von Logos aus der ganzen Welt verwendet, um Handelsmarken in Bildern oder Videos zu identifizieren. Mit diesem Feature können Sie beispielsweise ermitteln, welche Marken in sozialen Medien besonders beliebt sind oder besonders gerne in Medien platziert werden.

Der Dienst „Maschinelles Sehen“ erkennt, ob in einem bestimmten Bild Markenlogos vorhanden sind. Falls ja, gibt er den Markennamen, eine Zuverlässigkeitsbewertung und die Koordinaten eines Begrenzungsrahmens um das Logo zurück.

Die integrierte Logodatenbank deckt bekannte Marken aus Bereichen wie u.a. Unterhaltungselektronik und Bekleidung ab. Wenn Sie feststellen, dass die gesuchte Marke nicht vom Dienst „Maschinelles Sehen“ erkannt wird, ist es möglicherweise besser, mit dem Dienst [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) einen eigenen Logodetektor zu erstellen und zu trainieren.

## <a name="brand-detection-example"></a>Beispiel für die Markenerkennung

Die folgenden JSON-Antworten veranschaulichen, was vom Dienst „Maschinelles Sehen“ beim Erkennen von Objekten im Beispielbild zurückgegeben wird.

![Ein graues Sweatshirt mit Microsoft-Schriftzug und -Logo](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
In einigen Fällen wird der Markendetektor sowohl das Logobild als auch den stilisierten Markennamen als zwei separate Logos auswerten.

![Ein rotes T-Shirt mit Microsoft-Schriftzug und -Logo](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>Verwenden der API

Die Funktion zur Erkennung von Marken ist Teil der [Bildanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)-API. Sie können diese API über ein natives SDK oder REST-Aufrufe aufrufen. Beziehen Sie `Brands` in den Abfrageparameter **visualFeatures** ein. Nachdem Sie die vollständige JSON-Antwort erhalten haben, analysieren Sie einfach die Zeichenfolge auf den Inhalt im Abschnitt `"brands"`.

* [Schnellstart: Analysieren eines Bilds (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Schnellstart: Analysieren eines Bilds (REST-API)](./quickstarts/csharp-analyze.md)
