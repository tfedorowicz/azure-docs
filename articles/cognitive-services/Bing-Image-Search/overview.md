---
title: What is the Bing Image Search API?
titleSuffix: Azure Cognitive Services
description: The Bing Image Search API enables you to use Bing's cognitive image search capabilities in your application. By sending user search queries with the API, you can get and display relevant and high-quality images similar to Bing Images.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 01/05/2022
ms.author: aahi
#Customer intent: As a developer, I want to integrate Bing's image search capabilities into my app, so that I can provide relevant, engaging images to my users.
ms.custom: seodec2018
---

# What is the Bing Image Search API?

[!INCLUDE [Bing move notice](../bing-web-search/includes/bing-move-notice.md)]

The Bing Image Search API enables you to use Bing's image search capabilities in your application. By sending search queries to the API, you can get high-quality images similar to [bing.com/images](https://www.bing.com/images).

While the Bing Image Search API provides image-only search results, you can combine or use the other available [Bing Search APIs](../bing-web-search/bing-api-comparison.md) to find many types of content on the web.

## Bing Image Search features

| Feature                                                                                                                                                                                 | Description                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Suggest search terms in real-time](./concepts/bing-image-search-sending-queries.md) | Improve your app experience by using the [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) to display suggested search terms as they're typed. |
| [Filter and restrict image results](./concepts/bing-image-search-get-images.md)                       | Filter the images that Bing returns by editing query parameters.                                                                                                       |
| [Crop, resize, and display thumbnails](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Edit and display thumbnail previews for the images returned by Bing Image Search.                                                                                      |
| [Pivot & expand user search queries](./concepts/bing-image-search-sending-queries.md)               | Expand your search capabilities by including and displaying Bing-suggested search terms to queries.                                                                    |
| [Get trending images](trending-images.md)                                                                     | Customize a search for trending images from around the world.                                                                                                          |

## Workflow

The Bing Image Search API is a RESTful web service, making it easy to call from any programming language that can make HTTP requests and parse JSON. You can use the service using either the [REST API](./quickstarts/csharp.md), or the [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

1. Create a [Cognitive Services API account](../cognitive-services-apis-create-account.md) with access to the Bing Search APIs. If you don't have an Azure subscription, you can [create an account](https://azure.microsoft.com/free/cognitive-services/) for free.
2. Send a request to the API, with a valid [search query](./concepts/bing-image-search-sending-queries.md).
3. Process the API response by parsing the returned JSON message.

## Next steps

First, try the Bing Image Search API [interactive demo](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/).
This demo shows how you can quickly customize a search query and scour the web for images.

To quickly get started with your first API request, you can learn to:

* [Send search queries to Bing](./quickstarts/csharp.md) using the REST API, or
* [Request and filter](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) the images Bing returns using the SDK.

## See also

* [Pricing details](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) for the Bing Search APIs. 

* The [Bing Image Search API v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) reference section contains information on the API's endpoints, headers, API responses, and query parameters.

* The [Bing Use and Display Requirements](../bing-web-search/use-display-requirements.md) specify acceptable uses of the content and information gained through the Bing search APIs.

* The [Getting images from the web with the Bing Image Search API](./concepts/bing-image-search-get-images.md) article describes how to search and get images from the web.

* The [Sending and working with search queries](./concepts/bing-image-search-sending-queries.md) article describes how to make, customize, and pivot search queries.

* Visit the [Bing Search API hub page](../bing-web-search/overview.md) to explore the other available APIs.
