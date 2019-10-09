# Setup

0.  Install MongoDB and run it.
1.  Create npm package.json

        npm init -y

1.  Install dependancies: _Express_ and _Mongoose_

        npm i express mongoose

1.  Install dev dependancies: _dotenv_ and _nodemon_

        npm i --save-dev dotenv nodemon

1.  Edit scripts in pakage.json

    "scripts": {
    "devStart": "nodemon server.js"
    },

1.  Create `.env` and `.gitignore` files in root.

1.  Edit `.gitignore` file.

            .env
            node_modules

1.  Create `server.js` file in root.
1.  Edit `server.js` to connect app to server.

            const express = require('express')
            cons app = express()

            app.listen(3000, () => console.log("Server Started"))

1.  Run server

            npm run devStart

1.  Edit `server.js` to connect mongoose to mongodb.

            const express = require("express");
            const app = express();
            const mongoose = require("mongoose");

            // Handles deprecation warnings
            mongoose.set("useNewUrlParser", true);
            mongoose.set("useUnifiedTopology", true);

            mongoose.connect("mongodb://localhost/subscribers");

            const db = mongoose.connection;
            db.on("error", error => console.error(error));
            db.once("open", () => console.log("Connected to Database"));

            app.listen(3000, () => console.log("Server Started"));

1.  Edit `.env` file for mongodb URL

            DATABASE_URL=mongodb://localhost/subscribers

1.  Edit `server.js` to use `.env`

    require("dotenv").config();

            const express = require("express");
            const app = express();
            const mongoose = require("mongoose");

            // Handles deprecation warnings
            mongoose.set("useNewUrlParser", true);
            mongoose.set("useUnifiedTopology", true);

            mongoose.connect(process.env.DATABASE_URL);

            const db = mongoose.connection;
            db.on("error", error => console.error(error));
            db.once("open", () => console.log("Connected to Database"));

            app.listen(3000, () => console.log("Server Started"));

1.  Edit `server.js` to use json

            require("dotenv").config();

            const express = require("express");
            const app = express();
            const mongoose = require("mongoose");

            // Handles deprecation warnings
            mongoose.set("useNewUrlParser", true);
            mongoose.set("useUnifiedTopology", true);

            mongoose.connect(process.env.DATABASE_URL);

            const db = mongoose.connection;
            db.on("error", error => console.error(error));
            db.once("open", () => console.log("Connected to Database"));

            app.use(express.json())

            app.listen(3000, () => console.log("Server Started"));

1.  Edit server.js to create api routes

            require("dotenv").config();

            const express = require("express");
            const app = express();
            const mongoose = require("mongoose");

            // Handles deprecation warnings
            mongoose.set("useNewUrlParser", true);
            mongoose.set("useUnifiedTopology", true);

            mongoose.connect(process.env.DATABASE_URL);

            const db = mongoose.connection;
            db.on("error", error => console.error(error));
            db.once("open", () => console.log("Connected to Database"));

            app.use(express.json());

            const subscribersRouter = require('./routes/subscribers')

            app.listen(3000, () => console.log("Server Started"));

1.  Create `routes` folder in root and `subscribers.js` in `routes` folder.
1.  Edit `server.js` to use `subscribers.js` when the base URL is followed with `/subscribers`.

            require("dotenv").config();

            const express = require("express");
            const app = express();
            const mongoose = require("mongoose");

            // Handles deprecation warnings
            mongoose.set("useNewUrlParser", true);
            mongoose.set("useUnifiedTopology", true);

            mongoose.connect(process.env.DATABASE_URL);

            const db = mongoose.connection;
            db.on("error", error => console.error(error));
            db.once("open", () => console.log("Connected to Database"));

            app.use(express.json());

            const subscribersRouter = require('./routes/subscribers')
            app.use('/subscribers', subscribersRouter)

            app.listen(3000, () => console.log("Server Started"));

1.  Edit `subscribers.js` for routing template

            const express = require("express");
            const router = express.Router();

            // Getting all
            router.get('/', (req, res) => {

            })
            // Getting one
            router.get('/:id', (req, res) => {

            })
            // Creating one
            router.post('/', (req, res) => {

            })
            // Updating one
            router.patch('/:id', (req, res) => {

            })
            // Deleting one
            router.delete('/:id', (req, res) => {

            })

            module.exports = router;

1.  Install `REST Client` VSCode extension
1.  Edit `subscribers.js` to test a endpoint

            ...
            // Getting all
            router.get("/", (req, res) => {
            res.send("Hello World");
            });
            // Getting one
            router.get("/:id", (req, res) => {
            res.send(req.params.id);
            });
            ...

1.  Creat `route.rest` in `routes` folder. Clicking `send request` will show the results.

            // Getting all
            GET http://localhost:3000/subscribers

            ###
            // Getting one
            GET http://localhost:3000/subscribers/15

1.  Create `models` folder. Create `subscibers.js` in `models` folder.
1.  Edit `subscribers.js` in `models` folder.

            const mongoose = require("mongoose");

            const subscriberSchema = new mongoose.Schema({
            name: {
                type: String,
                required: true
            },
            subscribedToChannel: {
                type: String,
                required: true
            },
            subscribeData: {
                type: Date,
                required: true,
                default: Date.now
            }
            });

            module.exports = mongoose.model("Subscriber", subscriberSchema);

1.  Edit `subscribers.js` in `routes` foler.

            ...
            const Subscriber = require("../models/subscribers");
            ...
            // Getting all
            router.get("/", async (req, res) => {
            try {
                const subscribers = await Subscriber.find();
                res.json(subscribers);
            } catch (error) {
                res.status(500).json({ message: error.message });
            }
            });
            ...
            // Creating one
            router.post("/", async (req, res) => {
            const subscriber = new Subscriber({
                name: req.body.name,
                subscribedToChannel: req.body.subscribedToChannel
            });
            try {
                const newSubscriber = await subscriber.save();
                res.status(201).json(newSubscriber);
            } catch (error) {
                res.status(400).json({ message: error.message });
            }
            });
            ...

1.  Edit `route.rest`

            ...
            ###
            POST http://localhost:3000/subscribers
            Content-Type: application/json

            {
                "name": "Amaing Person",
                "subscribedToChannel": "Web Dev Simplified"
            }

1.  Create middleware in `routes/subscribers.js`

            ...
            async function getSubscriber(req, res, next) {
            let subscriber;
            try {
                subscriber = await Subscriber.findById(req.params.id);
                if (subscriber == null) {
                return res.status(404).json({ message: "Cannot find sbuscriber" });
                }
            } catch (error) {
                return res.status(500).json({ message: error.message });
            }
            res.subscriber = subscriber;
            next();
            }
            ...

1.  Test middleware

            --routes/subscribers--
            ...
            // Getting one
            router.get("/:id", getSubscriber, (req, res) => {
            res.send(res.subscriber.name);
            });
            ...

            --routes/route.rest
            ...
            ###
            // Getting one
            GET http://localhost:3000/subscribers/5d9e3b7ab661783a90847e8a
            ...
