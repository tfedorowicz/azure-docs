---
title: Set up an indexer connection to Azure Cosmos DB via a managed identity
titleSuffix: Azure Cognitive Search
description: Learn how to set up an indexer connection to an Azure Cosmos DB account via a managed identity
author: gmndrg
ms.author: gimondra
manager: liamca

ms.service: cognitive-search
ms.topic: how-to
ms.date: 09/19/2022
ms.custom: subject-rbac-steps, ignite-2022
---

# Set up an indexer connection to Azure Cosmos DB via a managed identity

This article explains how to set up an indexer connection to an Azure Cosmos DB database using a managed identity instead of providing credentials in the connection string.'

You can use a system-assigned managed identity or a user-assigned managed identity (preview). Managed identities are Azure Active Directory logins and require Azure role assignments to access data in Azure Cosmos DB. 

## Prerequisites

* [Create a managed identity](search-howto-managed-identities-data-sources.md) for your search service.

* [Assign a role](search-howto-managed-identities-data-sources.md#assign-a-role) in Azure Cosmos DB.

  For data reader access, you'll need the **Cosmos DB Account Reader** role and the identity used to make the request. This role works for all Azure Cosmos DB APIs supported by Cognitive Search. This is a control plane RBAC role. 

  At this time, Cognitive Search obtains keys with the identity and uses those keys to connect to the Azure Cosmos DB account. This means that [enforcing RBAC as the only authentication method in Azure Cosmos DB](../cosmos-db/how-to-setup-rbac.md#disable-local-auth) isn't supported when using Search with managed identities to connect to Azure Cosmos DB.

* You should be familiar with [indexer concepts](search-indexer-overview.md) and [configuration](search-howto-index-cosmosdb.md).

## Create the data source

Create the data source and provide either a system-assigned managed identity or a user-assigned managed identity (preview) in the connection string. 

### System-assigned managed identity

The [REST API](/rest/api/searchservice/create-data-source), Azure portal, and the [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) support using a system-assigned managed identity. 

When you're connecting with a system-assigned managed identity, the only change to the data source definition is the format of the "credentials" property. You'll provide the database name and a ResourceId that has no account key or password. The ResourceId must include the subscription ID of Azure Cosmos DB, the resource group, and the Azure Cosmos DB account name.

* For SQL collections, the connection string doesn't require "ApiKind". 
* For MongoDB collections, add "ApiKind=MongoDb" to the connection string and use a preview REST API.
* For Gremlin graphs, add "ApiKind=Gremlin" to the connection string and use a preview REST API.

Here's an example of how to create a data source to index data from a storage account using the [Create Data Source](/rest/api/searchservice/create-data-source) REST API and a managed identity connection string. The managed identity connection string format is the same for the REST API, .NET SDK, and the Azure portal.

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "[my-cosmosdb-ds]",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "ResourceId=/subscriptions/[subscription-id]/resourceGroups/[rg-name]/providers/Microsoft.DocumentDB/databaseAccounts/[cosmos-account-name];Database=[cosmos-database];ApiKind=[SQL | Gremlin | MongoDB];"
    },
    "container": { "name": "[my-cosmos-collection]", "query": null },
    "dataChangeDetectionPolicy": null

 
}
```

### User-assigned managed identity (preview)

The 2021-04-30-preview REST API supports connections based on a user-assigned managed identity. When you're connecting with a user-assigned managed identity, there are two changes to the data source definition:

* First, the format of the "credentials" property is the database name and a ResourceId that has no account key or password. The ResourceId must include the subscription ID of Azure Cosmos DB, the resource group, and the Azure Cosmos DB account name.

  * For SQL collections, the connection string doesn't require "ApiKind". 
  * For MongoDB collections, add "ApiKind=MongoDb" to the connection string
  * For Gremlin graphs, add "ApiKind=Gremlin" to the connection string.

* Second, you'll add an "identity" property that contains the collection of user-assigned managed identities. Only one user-assigned managed identity should be provided when creating the data source. Set it to type "userAssignedIdentities".

Here's an example of how to create an indexer data source object using the [preview Create or Update Data Source](/rest/api/searchservice/preview-api/create-or-update-data-source) REST API:


```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "[my-cosmosdb-ds]",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "ResourceId=/subscriptions/[subscription-id]/resourceGroups/[rg-name]/providers/Microsoft.DocumentDB/databaseAccounts/[cosmos-account-name];Database=[cosmos-database];ApiKind=[SQL | Gremlin | MongoDB];"
    },
    "container": { 
        "name": "[my-cosmos-collection]", "query": null 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity": "/subscriptions/[subscription-id]/resourcegroups/[rg-name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[my-user-managed-identity-name]" 
    },
    "dataChangeDetectionPolicy": null
}
```

## Create the index

The index specifies the fields in a document, attributes, and other constructs that shape the search experience.

Here's a [Create Index](/rest/api/searchservice/create-index) REST API call with a searchable `booktitle` field:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

## Create the indexer

An indexer connects a data source with a target search index and provides a schedule to automate the data refresh. Once the index and data source have been created, you're ready to create and run the indexer. If the indexer is successful, the connection syntax and role assignments are valid.

Here's a [Create Indexer](/rest/api/searchservice/create-indexer) REST API call with an Azure Cosmos DB indexer definition. The indexer will run when you submit the request.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index"
    }
```

## Troubleshooting

If you recently rotated your Azure Cosmos DB account keys, you'll need to wait up to 15 minutes for the managed identity connection string to work.

Check to see if the Azure Cosmos DB account has its access restricted to select networks. You can rule out any firewall issues by trying the connection without restrictions in place.

## See also

* [Indexing via an Azure Cosmos DB for NoSQL](search-howto-index-cosmosdb.md)
* [Indexing via an Azure Cosmos DB for MongoDB](search-howto-index-cosmosdb-mongodb.md)
* [Indexing via an Azure Cosmos DB for Apache Gremlin](search-howto-index-cosmosdb-gremlin.md)
