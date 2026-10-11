---
description: >-
  Client API impersonation lets an authorized application API key make a request
  as a particular panel user.
icon: masks-theater
---

# Client API Impersonation

### Client API Documentation

The docs for the Reviactyl v26 Client API can be found by going to `/docs/api/client` on your panel.

{% hint style="warning" %}
You must sign in on the login page **before** accessing the docs.
{% endhint %}

```
https://reviactyl.test/docs/api
```

You can also visit the docs on the demo instance [here](https://demo.reviactyl.app/docs/api/client).

### Requirements

The request must use an application API key that:

* belongs to a root administrator;
* has **Read-only** permission for **Impersonation** to make read requests;
* has **Read-write** permission for **Impersonation** to make write requests.

Set this on the application's API key in the admin panel under **API Keys → Create API Key → Permissions → Impersonation**. The selector offers **None**, **Read-only**, and **Read-write**. The stored `r_impersonation` values are `0`, `1`, and `3`, respectively. The middleware checks for read permission on every impersonated request, then checks write permission for non-read-only HTTP methods; therefore, choose **Read-write** for write requests.

### Making a request

Send the target user's numeric database ID or UUID in the `X-Execution-User` header on a client API request. For example, to list that user's servers:

```bash
curl --request GET \
  --url "https://reviactyl.test/api/client" \
  --header "Authorization: Bearer YOUR_APPLICATION_API_KEY" \
  --header "Accept: application/json" \
  --header "X-Execution-User: TARGET_USER_ID_OR_UUID"
```

The response is the client API response for the target user. The application key's owner remains the authenticated caller for authorization and auditing; the target user is used as the request's acting user.

### Errors

* `400 Bad Request`: an application API key called a client API endpoint without `X-Execution-User`.
* `403 Forbidden`: the caller is not a root administrator, the key lacks impersonation permission, or a read-only key attempted a write request.
* `404 Not Found`: the supplied user ID or UUID does not identify a user.

Only send this header with an application API key when impersonation is intended. Keep API keys secret and use the minimum permission level needed.
