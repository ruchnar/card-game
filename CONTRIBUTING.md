# Project architecture

The project is divided (as of now) into two main sections: *backend* and *frontend*, each with their quirks.

## Backend architecture

We use [Clojure](https://www.braveclojure.com) as the backend language for pretty much **everything** due to basically two reasons:

1. Because of the "everything is immutable" paradigm, it's very easy to reason about the logic of the code, which will probably get very complex as time goes on.
2. Due to its functional nature, it is *very* easy to run tests on it and a strict TDD discipline.

The backend also uses [Redis](https://redis.io) as a key-value storage mechanism for any persistent information necessary (such as the state of a game) since we will *always* know exactly what key we're searching for (we'll know the game ID, player ID, etc.).

Finally, the backend uses [Compojure](https://github.com/weavejester/compojure) to define it's [REST API](http://www.restapitutorial.com), which is the way the *frontend* will communicate with it.

## Frontend architecture

Frontend uses a standard [Vue.js](https://vuejs.org) architecture due to being easy to set up and not overcomplicating the frontend aspects.

# Project structure

The structure of a project (where the f*** is that file?) might be more important than it's architecture. In this case, there is a three-way split: *backend* and *frontend* as before, but we also have de *e2e* tests to worry about.

But some things also happen at the root:

- **docker-compose.yml**: Defines how the different parts of the project launch and interact with each other.
- **.travis.yml**: Defines how the different pars of the project are to be tested.

However, we will understand this further in the following sections.

## Backend structure

- **backend/Dockerfile**: Defines how the Docker image for the backend will be built and run
- **backend/project.clj**: Defines the project dependencies and entry point
- **backend/test/card_game/**: Contains all the unit tests for the code. Remember that a unit test must be done *before* the actual code.
- **backend/src/card_game/**: Contains the code that will run in production, the actual logic.
- **backend/src/card_game/persistence.clj**: This is the only file to interact directly with the database. All database calls *must* pass through here.
- **backend/src/card_game/api_handler.clj**: This is the only file to interact directly with frontend. Any actions to take as a response to a frontend call *must* be defined here.

## Frontend structure

- **frontend/Dockerfile** and **frontend/entry.sh**: Defines the Docker image and its entrypoint.
- **frontend/package.json** and **frontend/yarn.lock**: Define the project dependencies (`yarn.lock` is autogenerated by `yarn`)
- **frontend/test/unit/**: Contains all the unit tests for the code. Remember that a unit test must be done *before* the actual code.
- **frontend/public/**: The "website", with `index.html` being the entrypoint, but the real magic happens elsewhere...
- **frontend/src/**: Contains the code that will run in production, with its own structure.
- **frontend/src/main.js**: The official entrypoint for Vue. Should only be modified if we add a dependency that works through `Vue.use(dependency)`
- **frontend/src/App.vue**: The main Vue component. Since we're mostly using everything through `router` this mainly hosts the `<router-view/>` and little else.
- **frontend/src/router.js**: Here the "routes" are defined, which is the way Vue interprets what component or view to serve based on the URL (for example `http://frontend:8080/#/create-game` points to the CreateGame component)
- **frontend/src/components/** and **frontend/src/views**: The application logic, separated into "views" and "components" following no good criteria as of yet, we will improve on this in the future.


# Contributing guidelines

For any change or contribution, this is what is expected of you:

1. Announce within an issue that you want it assigned to you (or open said issue yourself).
2. Do not take on more than one issue at a time
3. Design the tests for your issue (unit tests always and in some cases e2e tests may be required)
4. Make them pass
5. Ask for a review from a collaborator
6. If changes are requested or travis does not pass, go to 3.

We will value the following things on your contribution *in this order*:

1. The tests pass
2. The code has tests
3. The tests are well-designed
4. The code is readable
5. The code is well-organized
6. The code works and fixes the issue

If at any point you have a question, please ask it if possible through a comment so others may benefit from the answer.