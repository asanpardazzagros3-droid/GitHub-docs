---
title: Getting started with the REST API
shortTitle: Getting started
intro: 'Learn how to use the {% data variables.product.github %} REST API.'
versions:
  fpt: '*'
  ghes: '*'
  ghec: '*'
redirect_from:
  - /rest/guides/getting-started-with-the-rest-api
  - /rest/initialize-the-repo
  - /rest/overview/resources-in-the-rest-api
  - /rest/using-the-rest-api/resources-in-the-rest-api
  - /v3/media
  - /rest/overview/media-types
  - /rest/using-the-rest-api/media-types
category:
  - Learn about the REST API
---

## Introduction

This article describes how to use the {% data variables.product.github %} REST API with {% data variables.product.prodname_cli %}, `curl`, or JavaScript. For a quickstart guide, see [AUTOTITLE](/rest/quickstart).

{% curl %}

{% ifversion ghec %}

Examples in this article send requests to `{% data variables.product.rest_url %}`. If you access {% data variables.product.github %} at a different domain, such as `{% data variables.enterprise.data_residency_example_domain %}`, the endpoint for API requests will reflect that domain. For example: `https://api.octocorp.ghe.com/`.

{% endif %}

{% endcurl %}

## About requests to the REST API

This section describes the elements that make up an API request:

* [HTTP method](#http-method)
* [Path](#path)
* [Headers](#headers)
* [Media types](#media-types)
* [Authentication](#authentication)
* [Parameters](#parameters)

Every request to the REST API includes an HTTP method and a path. Depending on the REST API endpoint, you might also need to specify request headers, authentication information, query parameters, or body parameters.

The REST API reference documentation describes the HTTP method, path, and parameters for every endpoint. It also displays example requests and responses for each endpoint. For more information, see the [REST reference documentation](/rest).

### HTTP method

The HTTP method of an endpoint defines the type of action it performs on a given resource. Some common HTTP methods are `GET`, `POST`, `DELETE`, and `PATCH`. The REST API reference documentation provides the HTTP method for every endpoint.

For example, the HTTP method for the ["List repository issues" endpoint](/rest/issues/issues#list-repository-issues) is `GET`."

Where possible, the {% data variables.product.github %} REST API strives to use an appropriate HTTP method for each action.

* `GET`: Used for retrieving resources.
* `POST`: Used for creating resources.
* `PATCH`: Used for updating properties of resources.
* `PUT`: Used for replacing resources or collections of resources.
* `DELETE`: Used for deleting resources.

### Path

Each endpoint has a path. The REST API reference documentation gives the path for every endpoint. For example, the path for the ["List repository issues" endpoint](/rest/issues/issues#list-repository-issues) is `/repos/{owner}/{repo}/issues`.

The curly brackets `{}` in a path denote path parameters that you need to specify. Path parameters modify the endpoint path and are required in your request. For example, the path parameters for the ["List repository issues" endpoint](/rest/issues/issues#list-repository-issues) are `{owner}` and `{repo}`. To use this path in your API request, replace `{repo}` with the name of the repository where you would like to request a list of issues, and replace `{owner}` with the name of the account that owns the repository.

### Headers

Headers provide extra information about the request and the desired response. Following are some examples of headers that you can use in your requests to the {% data variables.product.prodname_dotcom %} REST API. For an example of a request that uses headers, see [Making a request](#making-a-request).

#### `Accept`

Most {% data variables.product.prodname_dotcom %} REST API endpoints specify that you should pass an `Accept` header with a value of `application/vnd.github+json`. The value of the `Accept` header is a media type. For more information about media types, see [Media types](#media-types).

#### `X-GitHub-Api-Version`

You should use this header to specify a version of the REST API to use for your request. For more information, see [AUTOTITLE](/rest/overview/api-versions).

{% ifversion fpt or ghec %}

#### `User-Agent`

All API requests must include a valid `User-Agent` header. The `User-Agent` header identifies the user or application that is making the request.

{% cli %}

By default, {% data variables.product.prodname_cli %} sends a valid `User-Agent` header. However, {% data variables.product.prodname_dotcom %} recommends using your {% data variables.product.github %} username, or the name of your application, for the `User-Agent` header value. This allows {% data variables.product.prodname_dotcom %} to contact you if there are problems.

{% endcli %}

{% curl %}

By default, `curl` sends a valid `User-Agent` header. However {% data variables.product.prodname_dotcom %} recommends using your {% data variables.product.github %} username, or the name of your application, for the `User-Agent` header value. This allows {% data variables.product.prodname_dotcom %} to contact you if there are problems.

{% endcurl %}

{% javascript %}

If you use the Octokit.js SDK, the SDK will send a valid `User-Agent` header for you. However, {% data variables.product.prodname_dotcom %} recommends using your {% data variables.product.github %} username, or the name of your application, for the `User-Agent` header value. This allows {% data variables.product.prodname_dotcom %} to contact you if there are problems.

{% endjavascript %}

The following is an example `User-Agent` for an app named `Awesome-Octocat-App`:

```shell
User-Agent: Awesome-Octocat-App
```

Requests with no `User-Agent` header will be rejected. If you provide an invalid `User-Agent` header, you will receive a `403 Forbidden` response.

{% endif %}

<!-- Anchor to maintain links to this heading -->
<a name="media-types"></a>

### Media types

You can specify one or more media types by adding them to the `Accept` header of your request. For more information about the `Accept` header, see [`Accept`](#accept).

Media types specify the format of the data you want to consume from the API. Media types are specific to resources, allowing them to change independently and support formats that other resources don't. The documentation for each {% data variables.product.prodname_dotcom %} REST API endpoint will describe the media types that it supports. For more information, see the [AUTOTITLE](/rest).

The most common media types supported by the {% data variables.product.prodname_dotcom %} REST API are `application/vnd.github+json` and `application/json`.

There are custom media types that you can use with some endpoints. For example, the REST API to manage [commits](/rest/commits/commits#get-a-commit) and [pull requests](/rest/pulls/pulls) support the media types `diff`, `patch`, and `sha`. The media types `full`, `raw`, `text`, or `html` are used by some other endpoints.

All custom media types for {% data variables.product.github %} look like this: `application/vnd.github.PARAM+json`, where `PARAM` is the name of the media type. For example, to specify the `raw` media type, you would use `application/vnd.github.raw+json`.

For an example of a request that uses media types, see [Making a request](#making-a-request).

### Authentication

Many endpoints require authentication or return additional information if you are authenticated. Additionally, you can make more requests per hour when you are authenticated.

{% curl %}

To authenticate your request, you will need to provide an authentication token with the required scopes or permissions. There a few different ways to get a token: You can create a {% data variables.product.pat_generic %}, generate a token with a {% data variables.product.prodname_github_app %}, or use the built-in `GITHUB_TOKEN` in a {% data variables.product.prodname_actions %} workflow. For more information, see [AUTOTITLE](/rest/overview/authenticating-to-the-rest-api).

For an example of a request that uses an authentication token, see [Making a request](#making-a-request).

> [!NOTE]
> If you don't want to create a token, you can use {% data variables.product.prodname_cli %}. {% data variables.product.prodname_cli %} will take care of authentication for you, and help keep your account secure. For more information, see the [{% data variables.product.prodname_cli %} version of this page](/rest/guides/getting-started-with-the-rest-api?tool=cli).

> [!WARNING]
> Treat your access token the same way you would treat your passwords or other sensitive credentials. For more information, see [AUTOTITLE](/rest/overview/keeping-your-api-credentials-secure).

{% endcurl %}

{% cli %}

Although some REST API endpoints are accessible without authentication, {% data variables.product.prodname_cli %} requires you to authenticate before you can use the `api` subcommand to make an API request. Use the `auth login` subcommand to authenticate to {% data variables.product.github %}. For more information, see [Making a request](#making-a-request).

{% endcli %}

{% javascript %}

To authenticate your request, you will need to provide an authentication token with the required scopes or permissions. There a few different ways to get a token: You can create a {% data variables.product.pat_generic %}, generate a token with a {% data variables.product.prodname_github_app %}, or use the built-in `GITHUB_TOKEN` in a {% data variables.product.prodname_actions %} workflow. For more information, see [AUTOTITLE](/rest/overview/authenticating-to-the-rest-api).

For an example of a request that uses an authentication token, see [Making a request](#making-a-request).

> [!WARNING]
> Treat your access token the same way you would treat your passwords or other sensitive credentials. For more information, see [AUTOTITLE](/rest/overview/keeping-your-api-credentials-secure).

{% endjavascript %}

### Parameters

Many API methods require or allow you to send additional information in parameters in your request. There are a few different types of parameters: Path parameters, body parameters, and query parameters.

#### Path parameters

Path parameters modify the endpoint path. These parameters are required in your request. For more information, see [Path](#path).

#### Body parameters

Body parameters allow you to pass additional data to the API. These parameters can be optional or required, depending on the endpoint. For example, a body parameter may allow you to specify an issue title when creating a new issue, or specify certain settings when enabling or disabling a feature. The documentation for each {% data variables.product.prodname_dotcom %} REST API endpoint will describe the body parameters that it supports. For more information, see the [AUTOTITLE](/rest).

For example, the ["Create an issue" endpoint](/rest/issues/issues#create-an-issue) requires that you specify a title for the new issue in your request. It also allows you to optionally specify other information, such as text to put in the issue body, users to assign to the new issue, or labels to apply to the new issue. For an example of a request that uses body parameters, see [Making a request](#making-a-request).

You must authenticate your request to pass body parameters. For more information, see [Authentication](#authentication).

#### Query parameters

Query parameters allow you to control what data is returned for a request. These parameters are usually optional. The documentation for each {% data variables.product.prodname_dotcom %} REST API endpoint will describe any query parameters that it supports. For more information, see the [AUTOTITLE](/rest).

For example, the ["List public events" endpoint](/rest/activity/events#list-public-events) returns thirty issues by default. You can use the `per_page` query parameter to return two issues instead of 30. You can use the `page` query parameter to fetch only the first page of results. For an example of a request that uses query parameters, see [Making a request](#making-a-request).

## Making a request

{% cli %}

This section demonstrates how to make an authenticated request to the {% data variables.product.prodname_dotcom %} REST API using {% data variables.product.prodname_cli %}.

### 1. Setup

Install {% data variables.product.prodname_cli %} on macOS, Windows, or Linux. For more information, see [Installation](https://github.com/cli/cli#installation) in the {% data variables.product.prodname_cli %} repository.

### 2. Authenticate

1. To authenticate to {% data variables.product.github %}, run the following command from your terminal.

   ```shell
   gh auth login
   ```

   You can use the `--scopes` option to specify what scopes you want. If you want to authenticate with a token that you created, you can use the `--with-token` option. For more information, see the [{% data variables.product.prodname_cli %} `auth login` documentation](https://cli.github.com/manual/gh_auth_login).

1. Select where you want to authenticate to:

   * If you access {% data variables.product.github %} at {% data variables.product.prodname_dotcom_the_website %}, select **{% data variables.product.prodname_dotcom_the_website %}**.
   * If you access {% data variables.product.github %} at a different domain, select **Other**, then enter your hostname (for example: `octocorp.ghe.com`).

1. Follow the rest of the on-screen prompts.

   {% data variables.product.prodname_cli %} automatically stores your Git credentials for you when you choose HTTPS as your preferred protocol for Git operations and answer "yes" to the prompt asking if you would like to authenticate to Git with your {% data variables.product.prodname_dotcom %} credentials. This can be useful as it allows you to use Git commands like `git push` and `git pull` without needing to set up a separate credential manager or use SSH.

### 3. Choose an endpoint for your request

1. Choose an endpoint to make a request to. You can explore {% data variables.product.github %}'s [REST API documentation](/rest) to discover endpoints that you can use to interact with {% data variables.product.github %}.
1. Identify the HTTP method and path of the endpoint. You will send these with your request. For more information, see [HTTP method](#http-method) and [Path](#path).

   For example, the ["Create an issue" endpoint](/rest/issues/issues#create-an-issue) uses the HTTP method `POST` and the path `/repos/{owner}/{repo}/issues`.

1. Identify any required path parameters. Required path parameters appear in curly brackets `{}` in the path of the endpoint. Replace each parameter placeholder with the desired value. For more information, see [Path](#path).

   For example, the ["Create an issue" endpoint](/rest/issues/issues#create-an-issue) uses the path `/repos/{owner}/{repo}/issues`, and the path parameters are `{owner}` and `{repo}`. To use this path in your API request, replace `{repo}` with the name of the repository where you would like to create a new issue, and replace `{owner}` with the name of the account that owns the repository.

### 4. Make a request with {% data variables.product.prodname_cli %}

Use the {% data variables.product.prodname_cli %} `api` subcommand to make your API request. For more information, see the [{% data variables.product.prodname_cli %} `api` documentation](https://cli.github.com/manual/gh_api).

In your request, specify the following options and values:

{%- ifversion not fpt %}
* **--hostname:** If you are authenticated to multiple accounts across {% data variables.product.github %} platforms, specify where you are making the request. For example: `--hostname {% data variables.enterprise.data_residency_example_domain %}`.
{%- endif %}
* **--method** followed by the HTTP method and the path of the endpoint. For more information, see [HTTP method](#http-method) and [Path](#path).
* **--header:**
  * **`Accept`:** Pass the media type in an `Accept` header. To pass multiple media types in an `Accept` header, separate the media types with a comma: `Accept: application/vnd.github+json,application/vnd.github.diff`. For more information, see [`Accept`](#accept) and [Media types](#media-types).
  * **`X-GitHub-Api-Version`:** Pass the API version in a `X-GitHub-Api-Version` header. For more information, see [`X-GitHub-Api-Version`](#x-github-api-version).
* **`-f`** or **`-F`** followed by any body parameters or query parameters in `key=value` format. Use the `-F` option to pass a parameter that is a number, Boolean, or null. Use the `-f` option to pass string parameters.

  Some endpoints use query parameters that are arrays. To send an array in the query string, use the query parameter once per array item, and append `[]` after the query parameter name. For example, to provide an array of two repository IDs, use `-f repository_ids[]=REPOSITORY_A_ID -f repository_ids[]=REPOSITORY_B_ID`.

  If you do not need to specify any body parameters or query parameters in your request, omit this option. For more information, see [Body parameters](#body-parameters) and [Query parameters](#query-parameters). For examples, see [Example request using body parameters](#example-request-using-body-parameters) and [Example request using query parameters](#example-request-using-query-parameters).
{%- ifversion not fpt %}
* **--hostname:** If you are authenticated to multiple accounts across {% data variables.product.github %} platforms, specify where you are making the request. For example: `--hostname {% data variables.enterprise.data_residency_example_domain %}`.
{%- endif %}

#### Example request

The following example request uses the ["Get Octocat" endpoint](/rest/meta/meta#get-octocat) to return the octocat as ASCII art.

```shell copy
gh api --method GET /octocat \
--header 'Accept: application/vnd.github+json' \
--header "X-GitHub-Api-Version: {{ defaultRestApiVersion }}"
```

#### Example request using query parameters

The ["List public events" endpoint](/rest/activity/events#list-public-events) returns thirty issues by default. The following example uses the `per_page` query parameter to return two issues instead of 30, and the `page` query parameter to fetch only the first page of results.

```shell copy
gh api --method GET /events -F per_page=2 -F page=1
--header 'Accept: application/vnd.github+json' \
```

#### Example request using body parameters

The following example uses the ["Create an issue" endpoint](/rest/issues/issues#create-an-issue) to create a new issue in {% ifversion ghes %}a specified{% else %}the octocat/Spoon-Knife{% endif %} repository.{% ifversion ghes %} Replace `REPO-NAME` with the name of the repository where you want to create a new issue, and replace `REPO-OWNER` with the name of the account that owns the repository.{% endif %} In the response, find the `html_url` of your issue, and navigate to your issue in the browser.

```shell copy
gh api --method POST /repos/{% ifversion ghes %}REPO-OWNER/REPO-NAME{% else %}octocat/Spoon-Knife{% endif %}/issues \
--header "Accept: application/vnd.github+json" \
--header "X-GitHub-Api-Version: {{ defaultRestApiVersion }}" \
-f title='Created with the REST API' \
-f body='This is a test issue created by the REST API' \
```

{% endcli %}

{% curl %}

This section demonstrates how to make an authenticated request to the {% data variables.product.prodname_dotcom %} REST API using `curl`.

### 1. Setup

You must have `curl` installed on your machine. To check if `curl` is already installed, run `curl --version` on the command line.

* If the output provides information about the version of `curl`, that means `curl` is installed.
* If you get a message similar to `command not found: curl`, that means `curl` is not installed. Download and install `curl`. For more information, see [the curl download page](https://curl.se/download.html).

### 2. Choose an endpoint for your request

1. Choose an endpoint to make a request t
