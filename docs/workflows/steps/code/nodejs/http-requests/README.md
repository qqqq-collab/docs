# Make HTTP Requests with Node.js

HTTP requests are fundamental to working with APIs or other web services. You can make HTTP requests to retrieve data from APIs, fetch HTML from websites, or do pretty much anything your web browser can do.

**Below, we'll review how to make HTTP requests using Node.js code on Pipedream.**

We'll use the [`axios` HTTP client](https://github.com/axios/axios) in the examples below, but [you can use any npm package you'd like](/workflows/steps/code/#using-npm-packages) on Pipedream, so feel free to experiment with other clients, too.

If you're new to HTTP, see our [glossary of HTTP terms](https://requestbin.com/blog/working-with-webhooks/#webhooks-glossary-common-terms) for a helpful introduction.

[[toc]]

## Basic `axios` usage notes

To use `axios` on Pipedream, you'll just need to require the `axios` npm package:

```javascript
const axios = require("axios");
```

You make HTTP requests by passing a [JavaScript object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects) to `axios` that defines the parameters of the request. For example, you'll typically want to define the HTTP method and the URL you're sending data to:

```javascript
{
  method: "GET",
  url: `https://swapi.co/api/films/`
}
```

`axios` returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises), which is just a fancy way of saying that it makes the HTTP request in the background (asynchronously) while the rest of your code runs. On Pipedream, [all asynchronous code must be run synchronously](https://docs.pipedream.com/workflows/steps/code/#running-asynchronous-code), which means you'll need to wait for the HTTP request to finish before moving on to the next step. You do this by adding an `await` in front of the call to `axios`.

**Putting all of this together, here's how to make a basic HTTP request on Pipedream:**

```javascript
const resp = await axios({
  method: "GET",
  url: `https://swapi.co/api/films/`
});
```

The response object `resp` contains a lot of information about the response: its data, headers, and more. Typically, you just care about the data, which you can access in the `data` property of the response:

```javascript
const resp = await axios({
  method: "GET",
  url: `https://swapi.co/api/films/`
});

// HTTP response data is in the data property
const data = resp.data;
```

Alternatively, you can access the data using [object destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring), which is equivalent to the above and preferred in modern JavaScript:

```javascript
const { data } = resp.data;
```

## Send a `GET` request to fetch data

Make a request to retrieve Star Wars films from the Star Wars API:

```javascript
const axios = require("axios");

// Make an HTTP GET request using axios
const resp = await axios({
  method: "GET",
  url: `https://swapi.co/api/films/`
});

// Retrieve just the data from the response
const { data } = resp;
```

[Copy this workflow to run this example on Pipedream](https://pipedream.com/@dylburger/make-an-http-get-request-to-the-star-wars-api-p_OKC2KA/edit).

## Send a `POST` request to submit data

POST sample JSON to [JSONPlaceholder](https://jsonplaceholder.typicode.com/), a free mock API service:

```javascript
const axios = require("axios");

// Make an HTTP GET request using axios
const resp = await axios({
  method: "POST",
  url: `https://jsonplaceholder.typicode.com/posts`
  data: {
    name: "Luke",
  }
});

// Retrieve just the data from the response
const { data } = resp;
```

When you make a `POST` request, you pass `POST` as the `method`, and include the data you'd like to send in the `data` object.

[Copy this workflow to run this example on Pipedream](https://pipedream.com/@dylburger/make-an-http-post-request-using-axios-p_o7CbpY/edit).

## Pass query string parameters to a `GET` request

Retrieve fake comment data on a specific post using [JSONPlaceholder](https://jsonplaceholder.typicode.com/), a free mock API service. Here, you fetch data from the `/comments` resource, retrieving data for a specific post by query string parameter: `/comments?postId=1`.

```javascript
const axios = require("axios");

// Make an HTTP GET request using axios
const resp = await axios({
  method: "GET",
  url: `https://jsonplaceholder.typicode.com/comments`,
  params: {
    postId: 1
  }
});

// Retrieve just the data from the response
const { data } = resp;
```

You should pass query string parameters using the `params` object, like above. When you do, `axios` automatically [URL-encodes](https://www.w3schools.com/tags/ref_urlencode.ASP) the parameters for you, which you'd otherwise have to do manually.

[Copy this workflow to run this code on Pipedream](https://pipedream.com/@dylburger/pass-query-string-parameters-to-an-http-get-request-p_xMCOvj/edit).

## Send a request with HTTP headers

You pass HTTP headers in the `headers` object of the `axios` request:

```javascript
const axios = require("axios");

// Make an HTTP GET request using axios
const resp = await axios({
  method: "POST",
  url: `https://jsonplaceholder.typicode.com/posts`,
  headers: {
    "Content-Type": "application/json"
  },
  data: {
    name: "Luke"
  }
});
```

[Copy this workflow to run this code on Pipedream](https://pipedream.com/@dylburger/send-an-http-request-with-headers-p_q6ClzO/edit).

## Use an HTTP proxy to proxy requests through another host

When you make HTTP requests to certain services, they might require you whitelist a set of IP addresses those requests come from. Often, this is to improve the security of the target service.

By default, HTTP requests made from Pipedream can come from a range of IP addresses. **If you need to make requests from a single IP address, you can route traffic through an HTTP proxy**:

```javascript
const axios = require("axios");

let httpsProxyAgent = require("https-proxy-agent");
const agent = new httpsProxyAgent(`http://${user}:${pass}@${host}:${port}`);

const config = {
  method: "GET",
  url,
  httpsAgent: agent
};

const resp = await axios.request(config);
```

**If you don't have access to an HTTP proxy, [reach out to our team](/support)**. We operate a proxy that you can use for HTTP requests made through Pipedream.

[Copy this workflow to run this code on Pipedream](https://pipedream.com/@dylburger/make-an-http-request-through-a-proxy-p_ezC6RD/edit).

## IP addresses for HTTP requests made from Pipedream workflows

By default, HTTP requests made from Pipedream can come from a large range of IP addresses. **If you need to restrict the IP addresses HTTP requests come from, you have two options**:

- [Use an HTTP proxy to proxy requests](#use-an-http-proxy-to-proxy-requests-through-another-host)
- If you don't need to access the HTTP response data, you can [use `$send.http()`](/destinations/http/) to send requests from a [limited set of IP addresses](/destinations/http/#ip-addresses-for-pipedream-http-requests).
