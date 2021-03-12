Categorizing the (anti)-patterns of RESTful API
---

# Categorized

|Technical                          |Non-technical                      |
|-----------------------------------|-----------------------------------|
|[Content negotiation](https://github.com/huntertran/concordia-thesis-topic/blob/main/justifications/ContentNegotiation/ContentNegotiation.md)|Entity Endpoint                    |
|Endpoint redirection               |Contextless Resource name          |
|Entity Linking                     |Non-hierarchical Nodes             |
|Response caching                   |Amorphous URI                      |
|                                   |Verbless URI                       |
|                                   |Singularized Nodes                 |
|                                   |Pertinent (Relevant) Documentation |
|(Forgetting Hypermedia)            |Breaking Self-descriptiveness      |
|(Ignoring Caching)                 |Ignoring status code               |
|(Ignoring MIME type)               |Using the wrong HTTP Verbs         |
|[**_Versioning API_**](https://github.com/huntertran/concordia-thesis-topic/blob/main/justifications/ApiVersioning/ApiVersioning.md)|**_Hardcoded Endpoints_**          |
|**_No timeout_**                   |                                   |
|**_POST-PUT-PATCH return_**        |                                   |
|**_Timezone for time data_**       |                                   |
|**_Pagination for list_**          |                                   |

# Not categorized

* Misusing Cookies
