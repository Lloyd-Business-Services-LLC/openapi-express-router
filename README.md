# OPENAPI-3P-EXPRESS-ROUTER-MAKER

Forked from Alexander Davies' [original](https://github.com/AlexanderDavies/openapi-express-router), this is an update to support OpenAPI version 3.1.0 and remove support for Swagger (ver. 2). I've also updated two deprecated methods used in the original.

A simple package to build and connect Express routes based on an OpenAPI 3 or OpenAPI 3.1 JSON spec.

# Prerequisites

- node >=10.22.0
- express >=4.0.0

# Installation

    npm install https://github.com/Lloyd-Business-Services-LLC/openapi-3p-express-router-maker

# Basic Usage

    const express = require('express');
    const { connectRoutes } = require('openapi-express-router');
    
    #import OpenAPI 3 or Swagger 2 spec, controllers and middleware
    const openApi = require('./api/open-api.json);
    const controllers = require('./api/controllers.js);
    const middleware = require('./api/middleware.js);

    app = express();

    const options = {
        controllers,
        middleware
    };

    const connect = connectRoutes(openApi, options);

    connect(app);
    app.listen(8888);

# Connecting Controllers

Controllers are uniquely identified in the Swagger 2 or OpenAPI 3 specification by the operationId i.e. the operationId must match the name of the controller.

    "paths": {
        "/health/ping": {
            "get": {
                "summary": "Health check route",
                "description": "Returns pong if the server is healthy",
                "operationId": "healthController"
            }
        }
    }

Controllers are connected via the options object and can be nested at any level e.g.

    const options = {
        controllers: {
            healthController = (req, res, next) => res.status(200).json({message: req.body.message})
        }
    }

    // OR

    const options = {
        controllers: {
            nestedControllers: {
                healthController = (req, res, next) => res.status(200).json({message: req.body.message})
            }
        }
    }

If no controller is found which matches the operationId the following error will be thrown:
`No controller found for ${pathsKey}/${pathKey} which matches operationId: ${operationId}`

# Connecting Middleware

Middleware functions are optional but can be added to the Swagger 2 or OpenAPI 3 spec using the x-middleware array property.

    "/health/ping": {
        "get": {
            "summary": "Health check route",
            "description": "Returns pong if the server is healthy",
            "operationId": "healthController",
            "x-middleware": [
                "healthMiddleware"
            ],
        }
    }

Like controllers, middleware are connected via the options object and can be nested at any level e.g.

    const options = {
        middleware: {
            nestedMiddleware: {
                healthMiddleware = (req, res, next) => {
                    req.body.message = 'pong';
                    next();
                }
            }
        }
    }
