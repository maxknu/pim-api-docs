# Catalogs for Apps <span class="label label-beta">Beta</span>

## Overview

This guide introduces the catalog feature and explains why using catalogs to retrieve Akeneo data.  
You will save time during development with catalogs because Akeneo PXM Studio manages your **product selection** and gives you direct access to the relevant data. You can also leverage our **data mapping** feature to get product data in your chosen format.  

### What's a catalog?

A catalog is a selection of products defined by one or several criteria (families, categories, etc.)

Catalogs are only created by apps and configured by Akeneo users from Akeneo PXM Studio. This feature is only visible if the app manages catalogs.

![Catalogs for apps](../img/apps/app-catalog-list.png)

### Why use catalogs to retrieve product data? 

Using Catalogs helps you better manage the product information you get from the Akeneo PXM Studio. 

Most of the time, developers must design, develop and maintain a filter interface to allow users to configure their product selection: which products must be considered and which don't. 
Using catalogs for apps **prevents you from adding this filtering interface to your app**. When you retrieve product information related to a catalog, you **only retrieve the data your app needs to process**. 

Moreover, with catalogs, you **don't have to master the entire PIM structure anymore** to deliver a relevant filtering interface, as the Akeneo PXM Studio already provides it to your users. 
![Product Selection](../img/apps/catalogs-product-selection.png)

### Limits

To ensure Akeneo PXM Studio remains stable, we added some limits to catalogs:
- Each app can create up to **15 catalogs**.
- A product selection can have up to **25 selection criteria**.

::: info 
To learn more about the functional scope, please visit our Help Center and read the [How to configure catalogs for Apps?](https://help.akeneo.com/serenity-connect-your-pim/how-to-configure-catalogs-for-apps)
:::

### Troubleshooting

#### Automatic deactivation of catalogs

When a product selection becomes invalid, e.g., a selected category no longer exists, the PIM automatically disables the catalog. 

In that case, your app receives an HTTP 200 response containing the following payload.

```json
{
  "error": "No products to synchronize. The catalog \"65f5a521-e65c-4d7b-8be8-1f267fa2729c\" has been disabled on the PIM side. Note that you can get catalogs status with the GET /api/rest/v1/catalogs endpoint."
}
```


### Next steps

- Learn [how to create and use catalogs](/apps/catalogs.html#getting-started-with-catalogs)
- Discover [how users configure catalogs](https://help.akeneo.com/pim/serenity/articles/how-to-connect-my-pim-with-apps.html#how-to-configure-catalogs-for-apps) in the Akeneo PXM Studio


## Getting started with catalogs

To create and use a catalog, your app has to send some requests to the Akeneo REST API. This guide describes how to use the catalog features with your app. 

### What you'll learn

After completing this tutorial, you'll be able to create and use catalogs to retrieve product data.

### Requirements

- You have access to an Akeneo PIM ([get your App developer starter kit](/apps/overview.html#app-developer-starter-kit))
- Your app already manages the authorization process

### Step 1: Ask for catalog scopes

To manage catalogs, you need to ask for at least 4 scopes:
- `read_products`
- `read_catalogs`
- `write_catalogs`
- `delete_catalogs`

In the documentation [Ask for authorizations](/apps/authentication-and-authorization.html#step-2-ask-for-authorizations), 
you can discover how to ask for scopes.

Once Akeneo users accept these scopes during the app connection, you can manage and use catalogs.

### Step 2: Create catalogs

Once your app is connected and gets the proper authorization, your app can start using catalogs. 

To do so, your app has to create a catalog using the [Create a new catalog](/api-reference.html#post_app_catalog) endpoint. 
When your app creates a catalog, the API returns its UUID. You will use this catalog UUID to get information about the catalog and retrieve related products.

::: tips
To help your users know how to configure a catalog, give it the most descriptive name possible.
:::

**By default, new catalogs are disabled and only users can enable a catalog.**
It means that once a user has enabled it, you won't be able to retrieve products for this catalog. 

<img class="img-responsive in-article" alt="Enable catalog field" src="../img/apps/app-catalog-enable-button.png" style="max-width: 600px;">

To help your users, you can redirect them directly to the catalog configuration interface on their Akeneo PXM Studio using the following URL structure:

``` http

https://my-pim.cloud.akeneo.com/connect/apps/v1/catalogs/{catalog_uuid}
```

At any moment, you can verify if a catalog is enabled by calling the [get catalog endpoint](/api-reference.html#get_app_catalog).

### Step 3: Get products using catalogs

Once you have an enabled catalog, paginate the related products using this [endpoint](/api-reference.html#get_app_catalog_products).

### Next steps
- Learn [how to use the product mapping feature](/getting-started/synchronize-pim-products-6x/welcome.html)
- Learn [how to synchronize Akeneo data](/getting-started/synchronize-pim-products-6x/welcome.html)
- Explore the [REST API reference](/api-reference-index.html) 


## Use the catalog product mapping

To further help you in your app development, you can use product mapping to push your data structure to Akeneo PIM and get pre-formatted product data. 

### What you'll learn

After completing this tutorial, you'll be able to push your JSON mapping schema to the PIM and get product data in the format you expect. 

### Requirements

- You have access to an Akeneo PIM ([get your App developer starter kit](/apps/overview.html#app-developer-starter-kit))
- Your app already manages the authorization process 
- You followed the [Getting started with catalogs](#getting-started-with-catalogs) steps

### Step 1: Define your JSON schema

The first step to using the mapping feature is determining the JSON schema you need to push to the Akeneo PIM to get mapped product data. 

::: info 
**JSON Schema is a declarative language that allows annotating and validating JSON documents.** It describes an existing data format, provides clear human- and machine-readable documentation, and allows to validate data which is useful for ensuring the quality of client-submitted data.
:::

To help you define your schema, we advise you to use this online validator pre-configured with our latest meta-schema: [jsonschemavalidator.net](https://www.jsonschemavalidator.net/s/D85OL1LE). The validator highlights errors if there are some or displays a success message if your schema matches all our meta-schema constraints. 

You can also download the latest meta-schema at this url: [product mapping meta-schema - v0.0.13 (May, 2023)](/mapping/product/0.0.13/schema)

JSON schema example: 

```
{
  "$id": "https://example.com/product",
  "$schema": "https://api.akeneo.com/mapping/product/0.0.12/schema",
  "$comment": "My schema !",
  "title": "Product Mapping",
  "description": "JSON Schema describing the structure of products expected by our application",
  "type": "object",
  "properties": {
    "uuid": {
      "title": "Product UUID",
      "type": "string"
    },
    "type": {
      "title": "Product type",
      "type": "string"
    },
    "sku": {
      "title": "SKU (Stock Keeping Unit)",
      "description": "Selling Partner SKU (stock keeping unit) identifier for the listing. \n SKU uniquely identifies a listing for a Selling Partner.",
      "type": "string"
    },
    "name": {
      "title": "Product name",
      "type": "string"
    },
    "body_html": {
      "title": "Description (textarea)",
      "description": "Product description in raw HTML",
      "type": "string",
      "minLength": 0,
      "maxLength": 255
    },
    "main_image": {
      "title": "Main image",
      "description": "Format: URI/link. Allowed extensions: .png, .jpg",
      "type": "string",
      "format": "uri",
      "pattern": ".*(png|jpg).*$"
    },
    "main_color": {
      "title": "Main color",
      "description": "The main color of the product, used by grid filters on your e-commerce website.",
      "type": "string"
    },
    "colors": {
      "title": "Colors",
      "description": "List of colors separated by a comma.",
      "type": "array",
      "items": {
        "type": "string",
        "enum": ["blue", "red", "green", "yellow"]
      }
    },
    "available": {
      "title": "Is available",
      "description": "Used to display when a product is out of stock on your e-commerce website.",
      "type": "boolean"
    },
    "price": {
      "title": "Price (€)",
      "type": "number",
      "minimum": 0,
      "maximum": 10000
    },
    "publication_date": {
      "title": "Publication date",
      "description": "Format: ISO 8601 standard. \nUsed to filter products that must be published on your e-commerce website depending on the current date.",
      "type": "string",
      "format": "date-time"
    },
    "certification_number": {
      "title": "Certification number",
      "type": "string",
      "pattern": "^([0-9]{5})-([0-9]):([0-9]{4})$"
    },
    "size_letter": {
      "title": "Size (letter)",
      "type": "string",
      "enum": ["S", "M", "L", "XL"]
    },
    "size_number": {
      "title": "Size",
      "type": "number",
      "enum": [36, 38, 40, 42]
    },
    "weight": {
      "title": "Weight (grams)",
      "type": "number",
      "minimum": 0
    },
    "categories_string": {
      "title": "Categories (string)",
      "type": "string"
    },
    "categories_array": {
      "title": "Categories (array)",
      "type": "array",
      "items": {
        "type": "string"
      }
    }
  },
  "required": [
    "sku", "name"
  ]
}
```

::: warning
Please note that we use the `required` properties to filter products. If a product has an empty value for a required target, we don't send it as it doesn't match your app requirements. E.g. if a product has no value for the attributes mapped with your sku and/or name target, you won't receive it when requesting mapped products. 
:::

### Step 2: Push your product mapping schema

Once your product mapping schema is ready, use the endpoint to [create or update the product mapping schema related to a catalog](/api-reference.html#put_app_catalogs_mapping_schema_product) to push your schema to the PIM and access the related configuration screen inside the PIM. 

### Step 3:  Get mapped product data 

Finally, get product data using the endpoint to [get the list of mapped products related to a catalog](/api-reference.html#get_app_catalog_mapped_products).

### Step 4: Test your implementation

To do so, please:
1. Log into your [developer sandbox](/apps/overview.html#app-developer-starter-kit)
2. Click `Connect`, `Connected apps`, `Catalogs`, then the name of your catalog
3. Go to the `Product mapping` tab 
4. Fill in the mapping and enable the catalog using the `Enable catalog` button in the catalog header

We use your product mapping schema to display a screen where your users will configure their catalog.
Here is an example of an interface displayed using a JSON schema:

![Example of a mapping interface](../img/apps/mapping-interface.png)

### Next steps
- Explore the [REST API reference](/api-reference-index.html) 
