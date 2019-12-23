
# Minutes

Minutes is a minimal fork of [agenda-rest](https://github.com/agenda/agenda-rest).

Scheduling as a Service, based on [Agenda](https://github.com/agenda/agenda)

Assuming all job types could be thought of as REST endpoints, scheduling could be offered as a service. `agenda-rest` does just that, introduce a URL, name it, agenda-rest will call it on the times that you specify.

## Installation
Install agenda-rest as a global package
```bash
npm install -g agenda-rest
```

## Usage
To launch the agenda-rest server, use the command line interface specifying the database host name and the database name
```bash
agenda-rest --dbhost localhost --dbname agenda
```

## Command Line Interface options

| Options             | Description                                                                                                             |
|---------------------|-------------------------------------------------------------------------------------------------------------------------|
| **`-d, --dbname`**  | [optional] Name of the Mongo database, default is **agenda**                                                            |
| **`-h, --dbhost`**  | [optional] Mongo instance's IP or domain name, default is **localhost**                                                 |
| **`-u, --dburi`**   | [optional] Full Mongo connection string. If specified, will override **--dbhost**, **--dbname**                         |
| **`-p, --port`**    | [optional] agenda-rest server port, default is **4040**                                                                 |
| **`-k, --key`**     | [optional] x-api-key to be expected in headers. If not specified, access to agenda-rest server would be unauthenticated |
| **`-t, --timeout`** | [optional] Timeout for request duration, default is **5000 ms**                                                         |

## APIs

### **GET `/api/job`**
Get a list of defined jobs

* Method: GET

### **POST `/api/job`**
Defines a new category of jobs

* Method: POST
* Data:
```javascript
{
    name,           // New job type's name
    url,            // koa-router style url
    method,         // (optional) Request type, default: POST
    callback: {     // (optional) to call with response after invocation
        url,
        method,
        headers
    }
}
```

### **PUT `/api/job/:jobName`**
Updates definition of a job category

* Method: PUT
* Data: same as POST `/api/job`

### **DELETE `/api/job/:jobName`**
Deletes job definition and cancels occurrences 

* Method: DELETE

### **POST `/api/job/once`** & **POST `/api/job/every`**
Schedule a job for single or multiple occurrences

* Method: POST
* Data:
```javascript
{
    name,           // Name of the type to create the instance from
    interval,       // Interval in which job should be invoked (human-interval, can also be a date string for 'once')
    data: {         // (optional) default: {}
        headers,    // Http headers, e.g. { Authorization: '<token>' }
        params,     // Path parameters, to replace `:param` notations in job definition's url
        query,      // Query parameters (?foo=bar&baz=qux)
        body        // Accompanying data sent along the request
    }
}
```

Callback, if present, would be invoked by the following object:
```javascript
{
    data: {
        // passed data object, same as above
    },
    response        // response from invocation
}
```

### **POST `/api/job/now`**
Like `once` and `every`, though without `interval`. Executes the job now.

### **POST `/api/job/cancel`**
Cancels (not to be confused with 'delete') any jobs matching the query

* Method: POST
* Data: Mongo query
```javascript
{
    name: "foo"
}
```
