# Emergency Communication Platform 
This is a work in progress emergency communication system design blueprint.
`EMCO` for short. It is the abbreviation of `Em`ergency `Co`mmunication

> NOTE: Everything is subject to change.


## EMCO Protocol
`EMCO-P` is an emergency communication protocol designed to be accessible for any type of situations.

Can be used with WebSocket, HTTP Post or radio frequency.

## EMCO Relay 
It's a service (hardware or online server) for relaying and saving messages. Can be mentioned as `EMCO-R`

Initially it is a web server but planned to be extended to work on hardware level too.

## EMCO Client
A Client can connect via EMCO Protocol. (`EMCO-P`)
There are 2 types of clients.
1. Hardware Clients.
2. Software clients.


## EMCO Network (Mesh System)
It's (ideally) a hardware to extend network communication, but clients can be used for this purpose as well.
Bridge devices can translate or transmit between protocols LoRa <-> TCP 
- LoRa => Wifi Bundle
- LoRa => Ethernet Bundle 


# EMCO Hardware
- Hand Terminal (Text Messenger)
- Access Point (LORA To TCP / TCP to LORA) + Capture Portal
- nRF24L01 
- LoRa 915, 868, 433
  

Basic Data Structure
```jsonc
{
    is:"PUBKEY"//Issuer
    sig:"SIGNATURE"
    tm:{ //Telemetry
        lt:"", //Latitude
        ln:"", //Longitude
        csp:"", //Coordinate source / provider & precision 
        ldtz:"", //ISO String
        ut:"" //Unix Time
        ac:"",//Country
        as:"",//State
        at:"",//Town (City)
        am:"",//Muncipality
    },
    r:{//Request
        t:"",//Type
        c:"io.ohshift.emco.v1",//client
        m:"",//message
        sc:"",//shortcode
        th:"",//Thread ID 
    },
    i:{//Identification
    //This is an object for a client/user to introduce itself.
    //Can contain informations like; 
    // - name, 
    // - photo, 
    // - location, 
    // - bio, 
    // - twitter, 
    // - title

    },
    e:{//Echo
        //Needs to be explored.
        is:"ISSUER"
        sig:"SIGNATURE"
        //Echo object should contain its own signature, 
        //Echo object should be ignored when calculating request signature
        //Echo message must not modify original request.
    }
}
```

| Key   | Type                        | Required | Description                                                                                | Sample                    |
|-------|-----------------------------|----------|--------------------------------------------------------------------------------------------|---------------------------|
| is    | Issuer                      | Yes      | 32-bytes lowercase hex-encoded public key of the event creator.                            |                           |
| sig   | Signature                   | Yes      | 64-bytes hex of the signature of the sha256 hash of the serialized event data.             |                           |
| tm    | Telemetry                   | ~        | Telemetry Data Object.                                                                     | {}                        |
| tm.lt | Latitude                    | No       | Latitude coordinate.                                                                       | 37.4459                   |
| tm.ln | Longitude                   | No       | Longitude coordinate.                                                                      | 43.7450                   |
| csp   | Coordinate Source Precision | No       | 2 Character identifies source of the coordinate and how precise.                           | MA (Mobile & Approximate) |
| ldtz  | Locale date time w/ zone    | Yes      | ISO Local date time with zone information. Not needed when ut is provided.                 | 2023-02-11T04:09:15.655Z  |
| ut    | Unix Time                   | Yes      | Can be used over ldtz.                                                                     | 1676087538810             |
| tm.ac | Country ISO 2 Chars         | No       | ISO 3166 ALPHA-2 Country Code                                                              | TR for Turkey             |
| tm.as | State Name                  | No       | State name abbreviation if available.                                                      | CA                        |
| tm.at | Town / City                 | No       | Hakkari                                                                                    | Hakkari                   |
| tm.am | Muncipality                 | No       | Next type of location under a city                                                         | Çukurca                   |
| r     | Request Object              | ~        |                                                                                            |                           |
| r.t   | Request Type                | Yes      | Single char request type. See request type table.                                          | P                         |
| r.c   | Request Client              | Yes      | Web service for resolving request contents with version info. See `Request Client` table.  | io.ohshift.emco.v1        |
| r.m   | Request Message             | No       | Optional text content for a request. Up to 320 characters.                                 | Fire, help!               |
| r.sc  | Short Code                  | No       | Optional short code for various situations. See Short Status Code Table                    |                           |
| r.th  | Thread Id                   | No       | If this message is an addition to another message, specify sig id of the previous message. |                           |
| i     | Identification Object       | No       | Client identification broadcast data.                                                      |                           |
| i.n   | Name                        | No       |                                                                                            |                           |
| i.t   | Title                       | No       |                                                                                            |                           |
| i.b   | Bio                         | No       | 320 Characters text                                                                        |                           |
| i.p   | Photo URL                   | No       | A web URL or encoded file                                                                  |                           |
| i.sm  | Twitter handle              | No       | Twitter handle                                                                             |                           |



## Coordinate Definitions
- Coordinate provider types
  - M: Mobile
  - I: ISP
  - G: GPS
- Precision types.
  - A: Approximate
  - P: Precise
  - U: Undetermined / Unkown


## Request Types
- P: Ping
- M: Message
- I: Identifier
- Q: Query

## Echo Data
Relayed messages are called echoes. I need to think more about this topic.

My intial thoughts are; 
1. If a request does not contain a location relay can add it's own location
2. Echo should contain a identifier like: It's hardware or a server etc.



## Short Codes (Needs to be extended.)
- A => ALL OK
- A0 => All good. / I'm ok
- A1 => I've been rescued.
- 
- C => Earthquake
- C1 => Under wreck
- 
- D => Flooding.
- D1 => Flooding trapped.
- 
- F => Fire
- F1 => Place I'm in is burning.
- 
- L => Life threatening situation
- L1 => Heart Attack
- L2 => Stabbed
- L3 => Being followed
- L4 => Need medicine
- L5 => Need water
- L6 => Need shelter
- L7 => Trapped
- 
- W: => Sea, Ocean related messages
- W1: => Stranded
- W2: => Crash
- 
- X: Emergency responses
- X0: Ping
- X1: Rescued
- 
- Z: Emergency responses
- Z0 Ping
- Z1: Request received
- Z2: En route
- Z3: Arrived
- Z4: Can't Find Location


### Mind Map - Topics to explore
- A Client can store, relay, if required add it's own location while relaying.
- Twitter Proxy? (@Mention a relay's twitter account to send message.)
- Tweet ID? Tweet fetcher.
- Heart Beat
- Automated communication (a smart device sending a request detected automatically like a smart watch maybe)
