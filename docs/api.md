Code Architecture
=================

The server code is separated in different folders:

    - constants: listing contants used for the server code (apiKeys ...)
    - controllers: contains every services strategies
    - middleware: contains middlewares
    - models: contains data models
    - routes: containes routes files
    - services: contains services functions used for the logi of the server
    - utils: some utility functions used by the server (data access code ...)

The server contains files at the root of the server for differents purposes:

    - .env: stores the api, db credentials
    - index.js: the main server file where everything is being reunited
    - packages.json: contains every dependencies for the server


The different routes:
=====================

Authentifiaction routes; here are the routes used for authentificating in the app:  

    /api/auth/register :  
    This route is used to register an account on the app.  
    It is a route for http post and it need two parameters in the body:  
        string  username  
        string  password  

    /api/auth/login :
    This route is used to login to an existing account on the app.
    It is a route for http post and it need two parameters in the body:
        string  username
        string  password


Area routes; here are the routes to manage areas :

    /api/areas/userId :
    This route is used to get every areas created by the user
    It is a get route and need one request parameter in the URL:
        string userId

    /api/auth/github/newarea:
    This route is used to post new areas
    It is a post route and need some body parameters:
        string userId
        string actionService
        string reactionService
        string actionName
        string reactionName
        string actionElemName
        string reactionElemName

    /api/areas/areasId :
    This route is used to delete areas
    It is a delete route and need one request parameter in the URL:
        string areasId


Clash of clans routes; some routes to query ClashOfClans API:

    /api/coc/player/:id
    This route is use to get players info
    It requires an id in the url

    /api/coc/clan/:id
    This route is use to get clans info
    It requires an id in the url


Discord Bot routes; some routes to use with our dicord bot:

    /api/discordbot/sendmessage/:msg
    This route is used to send a msg using our discord bot
    it needs a msg in the url

Discord routes; some routes to use discord authaurization:

    /api/auth/discord :
    This route is used to start the discord passport auth flow.
    http get

    /api/auth/discord/callback :
    This route is used by discord to send accessToken
    http get

    /api/auth/discord/discordat :
    This route is used by our server to get the accesstoken
    http put with parameters in the body
    string username
    string token

Github routes; some routes to use github authaurization:

    /api/auth/github :
    This route is used to start the github passport auth flow.
    http get

    /api/auth/discord/callback :
    This route is used by github to send accessToken
    http get

    /api/auth/error :
    Route called if error during the github auth flow
    http get

    /api/auth/github/githubat :
    This route is used by our server to get the accesstoken
    http put with parameters in the body
    string username
    string token
    string gitname

    /api/auth/github/webhook :
    This route is called by hookdeck when an event is triggered on github
    http post, in the body, you find informations about the event

Google routes; some routes to use google authaurization:

    /api/auth/google :
    This route is used to start the google passport auth flow.
    http get

    /api/auth/google/callback :
    This route is used by google to send accessToken
    http get

    /api/auth/google/googleat :
    This route is used by our server to get the accesstoken
    http put with parameters in the body
    string username
    string token

Imgur routes; some routes to use imgur authaurization:

    /api/auth/imgur :
    This route is used to start the imgur passport auth flow.
    http get

    /api/auth/imgur/callback :
    This route is used by imgur to send accessToken
    http get

    /api/auth/imgur/imgurat :
    This route is used by our server to get the accesstoken
    http put with parameters in the body
    string username
    string token

Office routes; some routes to use office authaurization:

    /api/auth/office :
    This route is used to start the office passport auth flow.
    http get

    /api/auth/office/callback :
    This route is used by office to send accessToken
    http get

Spotify routes; some routes to use spotify authaurization:

    /api/auth/spotify :
    This route is used to start the spotify passport auth flow.
    http get

    /api/auth/spotify/callback :
    This route is used by spotify to send accessToken
    http get

    /api/auth/spotify/spotifyat :
    This route is used by our server to get the accesstoken
    http put with parameters in the body
    string username
    string token

    /api/auth/likespotify
    This route is used to follow a playlist
    http put with parameter in the body
    string token

Twilio routes; some routes to use twilio api:

    /api/twilio/add-verified-number :
    http post with parameters in body to add a verified number to account
    string number

    /api/twilio/verify-number :
    http post with parameters in body to verify a number (code by sms)
    string phoneNumber
    string verifCode

    /api/twilio/sendsms :
    http post with parameters in body to send sms
    string receiver
    string message

OpenWether routes; some routes to query openweather api:

    /api/weather/:city
    http get with parameters in URL to get current temperature in a city
    string city



In the 'services' folder, there is a folder conrresponding to each service:

    /Area contains the functions to handle few Area related operations:
        -handle new area creation
        -handle reactions when an area is triggered
        -get areas for no auth services
    
    /Discord/DiscordBot contains the functions to handle few Discord operations:
        -Ban a user
        -Create a channel
        -Send a msg

    /Github contains the functions to handle few Github operations:
        -Create a webhook
        -Delete a webhook
        -Get user repository informations
        -Handle github action creation
        -Handle reaction triggered with an action using github as a service
        -Add a star to a repo
    
    /NoAuth contains the functions to handle few operations on different services:
        /ClashRoyal:
            -Get clan member count
            -Get player trophies count
            -Handle clash of clans action being triggered
        /OpenWeather:
            -Get city's temperature
            -Handle openweather action being triggered
    
    /Phone contains an object that is used to store the current phone username

    /Spotify contains the functions to handle a few Spotify operations:
        -Add a random song to a random playlist
        -Create a new playlist
        -Follow and random playlist
        -Handle spotify reactions
    
    /twilio.js contains the function to handle twilio's operations:
        -Send sms



