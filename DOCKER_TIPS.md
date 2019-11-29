Docker is a powerful tool when it comes to cross environment development & production. However, it can be hard to use when scaling large applications that are using multiple containers. That's when Docker Compose enters as the player 2 of the containerization game: it helps scripting the creation and management of containers while still leveraging the power of Docker.

What I am going to show you today is a little tip on how you can get the most of Docker Compose for your development workflow.

I'm going to take Node.js as an example but you don't need to know anything about Node.js, it is just an example to share with you this tip. I'll be showing a quick Rust example too.

Docker Compose Configuration
If you have been used to Node.js development with Docker Compose, you might know that one popular image to use for this matter is the official node image which is already including a bunch of tools such as, obviously, node (the executable to run & interpret JavaScript code) & npm (a tool to install Node.js packages).

And you might have built your docker-compose.yaml that way:
version: "3"

services:
    node:
        image: node
        user: node
        working_dir: /home/node/app
        tty: true
        stdin_open: true
        volumes:
            - .:/home/node/app
That is pretty much every Docker Compose configuration ever for creating a long-lasting container that will be used for all that matters. First we start our container in the background:
$ docker-compose up --detach
And then, we can use it for several commands like running a script:
$ docker-compose exec node node main.js
Or initializing a new NPM configuration:
$ docker-compose exec node npm init --yes
And even installing our favorite tools:
$ docker-compose exec node npm install --save-dev rollup
But see how we are repeating this little node thingy on each of our commands? And see how unnatural it is to run npm while using a container named node? Today I'm going to show you how you can use a different Docker Compose configuration to make our commands more natural and intuitive to use.

Enhancing The Provided API
First, we need two containers: one will be dedicated to the node executable, and the other for the npm one. And we don't even need to use two custom images for that matter because the official one already includes them. We can just re-use the node image twice like in the following configuration:
version: "3"

services:
    node:
        image: node
        user: node
        working_dir: /home/node/app
        entrypoint: node
        volumes:
            - .:/home/node/app

    npm:
        image: node
        user: node
        working_dir: /home/node/app
        entrypoint: npm
        volumes:
            - .:/home/node/app
You can see that those two containers have almost the exact same configuration, except for the entrypoint property. This is used to tell which command to append before running the command that will be issued. If this sounds a little hard to understand, you'll see that it is in reality a really simple trick. Note that we also removed the now uncessary stdin_open & tty properties.

This means that now, if we want to run some Node.js script, we can just do that instead:
$ docker-compose run node main.js
See how clear and nice it is now to use Docker Compose to run our Node.js script? Doesn't that feel more intuitive now? Another advantage to that is that the container will just stop as soon as the command (node main.js) stops. So that we don't forget to stop already running Docker containers that were running in the background (how many times have I been forced to search for which project I had a server running at the exact same port just to cancel it because I was to lazy to stop it yesterday, I'm sure it did happen to you too).

But it does not stop there since we can also run any npm commands with the same API:
$ docker-compose run npm install --save-dev rollup-plugin-terser
And just like that, we included another package. Do you start to feel how great this is? We could just continue and add whatever command we would like. This is why the entrypoint property is so great and allows us to construct a nice little API to leverage the power of our already awesome tools.

Anything Really
I highly focused on the JavaScript & Node.js ecosystem here, but you could use this little trick for about anything really. Whether it is Ruby, Python, C, Rust, etc... A-NY-THING.
version: "3"

services:
    node:
        image: node
        user: node
        working_dir: /home/node/app
        entrypoint: node
        volumes:
            - .:/home/node/app

    npm:
        image: node
        user: node
        working_dir: /home/node/app
        entrypoint: npm
        volumes:
            - .:/home/node/app

    cargo:
        image: rust
        user: 1000:1000
        working_dir: /usr/src/myapp
        entrypoint: cargo
        volumes:
            - .:/usr/src/myapp


$ docker-compose run cargo new awesome-wasm-thingy --bin
Here, we simply included the cargo command for using Rust to create a new subproject for generating our WebAssembly's next big thing. Just like that.