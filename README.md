## FieldDoc Data API (beta)

The FieldDoc API is organized around <a href="https://en.wikipedia.org/wiki/Representational_state_transfer" target="_blank">REST</a>. Our API has predictable resource-oriented URLs and returns <a href="https://www.json.org" target="_blank">JSON-encoded</a> responses. It uses standard <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Status" target="_blank">HTTP response codes</a>, authentication, and <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods" target="_blank">methods</a>.

## Caveats

As the current version is a read-only beta release, please be aware that we're still working on it. Aspects of this release are unstable and may change without notice as we measure usage, performance, and other feedback. Breaking changes may include modified URL structures and the removal or re-naming of JSON object properties. We recommend that you experiment first and proceed with caution before incorporating API data into production applications. As an early adopter you may receive periodic email updates as we make adjustments and introduce new functionality.

## Authentication

The FieldDoc API uses API keys to authenticate requests.

Your API keys carry many privileges so be sure to keep them secure! Do not share your API keys in publicly accessible areas such as GitHub, client-side code, and so forth.

## Errors

FieldDoc uses conventional HTTP response codes to indicate the success or failure of an API request. In general, codes in the `2xx` range indicate success; codes in the `4xx` range indicate that a request failed given the information provided (e.g. a required parameter was missing or invalid); and codes in the `5xx` range indicate an error with FieldDoc's servers.

## Base URL

All URLs referenced in this documentation have the following base:

```
https://api.fielddoc.org
```

The FieldDoc API is served over HTTPS. To ensure data privacy, unencrypted HTTP is not supported.

## Coordinate format

Geographic coordinates provided to the FieldDoc API should be formatted in the order `longitude, latitude` and specified as decimal degrees in the WGS84 coordinate system. This pattern matches the [GeoJSON](https://geojson.org) standard used throughout FieldDoc.

## Resources

* [Practices](#practices)

### Practices

#### List all practices

`GET /v1/practices`

Retrieve a list of **public** practices. Supports spatial search using territories, bounding boxes, or geographic point coordinates (proximity). When combining parameters, make sure to provide values that are logically consistent for your use case. Redundant or conflicting spatial filters may produce unexpected or empty result sets.

**Important:** In the current release, you must provide at least one of the following parameters to make a successful request: `organization`, `program`, or `territory`.

**Parameters**

| Name | Type| Description |
| :--- | :--- | :--- |
| `access_token`<br /><sub>required</sub> | string | Your FieldDoc access token. |
| `limit` | integer | The number of objects to be returned. Limit can range between 1 and 100. **Default:** 10. |
| `page` | integer | The result set page to be returned. **Default:** 1. |
| `organization` | integer | A numeric organization identifier. |
| `program` | string | A comma-separated list of one or more program identifiers. Example: `1,2,3,4` |
| `territory` | string | A normalized territory name. Example: Transform "Poconos and Kittatinny" into `poconos_and_kittatinny`. |
| `bbox` | string | A comma-separated list of bounding box coordinates in the format `minX,minY,maxX,maxY`. Example: `-76.616539,39.269442,-76.58255,39.291366` |
| `format` | string | Specify a different response format. Currently supports `geojson`, which returns a `FeatureCollection`. |
| `expand` | string | A comma-separated list of one or more object properties to include in the response. These properties are not included in responses by default. Example: `project` |

**Request**

```
GET https://api.fielddoc.org/v1/practices?access_token={token}&expand=project
```

**Standard Response**

```json
{
  "object": "list",
  "url": "/v1/practices",
  "data": [
    {
      "id": 1,
      "object": "practice",
      "geometry": {
        "coordinates": [
          -77.450575,
          37.537372
        ],
        "type": "Point"
      },
      "modified_at": "2022-09-30T12:00:00Z",
      "name": "Tree Planting",
      "project": {
        "id": 1,
        "name": "2022 Land Acquisitions",
        "object": "project",
        "status": "complete"
      },
      "territories": [
        "Brodhead",
        "Poconos and Kittatinny"
      ]
    },
    {
      "...": "..."
    },
    {
      "...": "..."
    }
  ]
}
```

**GeoJSON Response**

```json
{
  "features": [
    {
      "geometry": {
        "coordinates": [
          -77.450575,
          37.537372
        ],
        "type": "Point"
      },
      "properties": {
        "id": 1,
        "modified_at": "2022-09-30T12:00:00Z",
        "name": "Tree Planting",
        "project": {
          "id": 1,
          "name": "2022 Land Acquisitions",
          "object": "project",
          "status": "complete"
        },
        "object": "practice",
        "territories": [
          "Brodhead",
          "Poconos and Kittatinny"
        ]
      },
      "type": "Feature"
    },
    {
      "...": "..."
    },
    {
      "...": "..."
    }
  ],
  "type": "FeatureCollection"
}
```

#### Retrieve a practice

`GET /v1/practices/:id`

Retrieve a single practice.

**Parameters**

| Name | Type| Description |
| :--- | :--- | :--- |
| `access_token`<br /><sub>required</sub> | string | Your FieldDoc access token. |

**Request**

```
GET https://api.fielddoc.org/v1/practices/1?access_token={token}
```

**Response**

```json
{
  "completed_on": "2022-06-30",
  "created_at": "2022-01-01T00:00:00.000000Z",
  "description": "...",
  "drainage_geometry": {
    "coordinates": [],
    "type": "Polygon"
  },
  "extent": {
    "calculated": {
      "converted": 2.47,
      "metric": 10000
    },
    "custom": 0
  },
  "geometry": {
    "coordinates": [],
    "type": "Polygon"
  },
  "id": 1,
  "inspections": [],
  "metrics": {
    "features": [
      {
        "...": "..."
      }
    ]
  },
  "model_inputs": {
    "...": "..."
  },
  "modified_at": "2022-01-01T00:00:00.000000Z",
  "name": "Yards Creek Phase IV",
  "organization": {
    "id": 1,
    "name": "The Commons",
    "object": "organization"
  },
  "practice_type": {
    "geometry_type": null,
    "id": 1,
    "name": "Fee acquisition",
    "object": "practice_type",
    "unit": {
      "group": "area",
      "object": "unit",
      "plural": "acres",
      "singular": "acre",
      "symbol": "ac",
      "type": "unit",
      "usage": "1 acre"
    }
  },
  "program": {
    "id": 1,
    "name": "Delaware River Watershed Protection Fund - Forestland Capital Grants",
    "object": "program"
  },
  "project": {
    "id": 1,
    "name": "2022 Land Acquisitions",
    "object": "project",
    "status": "complete"
  },
  "territories": [
    {
      "category": "Focus Area",
      "id": 1,
      "name": "Paulinskill"
    },
    {
      "category": "Cluster",
      "id": 2,
      "name": "New Jersey Highlands"
    }
  ],
  "object": "practice"
}
```
