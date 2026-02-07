## Product Choice

1. Telegram
2. https://www.bing.com/ck/a?!&&p=a571247980e5b3d18ed5094d9c80257bd2566a7e11c835431a487c6e906a2b21JmltdHM9MTc3MDQyMjQwMA&ptn=3&ver=2&hsh=4&fclid=01ab19d5-7177-6a21-2d7d-0f9e705f6bbb&psq=telegram&u=a1aHR0cHM6Ly93ZWIudGVsZWdyYW0ub3JnLw
3. Telegram, cloud-based messaging app owned by Russian entrepreneurs Pavel and Nikolai Durov. Telegram users can exchange text messages, hold voice calls, share files, join groups of up to 200,000 members, and subscribe to public broadcast channels.

## Main components

1. ![Telegram Component Diagram](https://raw.githubusercontent.com/mariachizhikova08/lab-01-market-product-and-git/main/docs/diagrams/out/telegram/component-diagram/Component%20Diagram.svg)
2. https://raw.githubusercontent.com/mariachizhikova08/lab-01-market-product-and-git/refs/heads/main/docs/diagrams/src/telegram/component-diagram.puml
3. (and 4) Mobile App (iOS/Android) – Client-side application for end users; communicates with Telegram servers via MTProto protocol.
MTProto Gateway (DC Entry) – Entry point for client connections; handles initial handshake, encryption, and routing of MTProto traffic to internal services.
Message Handling Service – Core service responsible for processing, routing, and delivering messages (e.g., one-to-one, group, broadcast) across the system.
Auth & Session Service – Manages user authentication, session tokens, device registration, and secure session lifecycle (e.g., login/logout, multi-device sync).
State Cache (Redis) – In-memory cache layer used for fast access to frequently used data (e.g., user status, session info, message metadata), reducing DB load and latency.

## Data flow
1. ![Telegram Component Diagram](https://raw.githubusercontent.com/mariachizhikova08/lab-01-market-product-and-git/main/docs/diagrams/out/telegram/sequence-diagram/Sequence%20Diagram.svg)
2. https://raw.githubusercontent.com/mariachizhikova08/lab-01-market-product-and-git/refs/heads/main/docs/diagrams/src/telegram/sequence-diagram.puml
3. group "User sends a message"
4. The client encrypts and sends the message via MTProto to the MTProto Gateway.
The gateway validates the user’s session with the Auth & Session Service.
The message is forwarded to the Message Handling Service, which:
Persists it in the Sharded Chat DB,
Updates the State Cache (Redis) for fast delivery,
Notifies relevant recipients via the Notification/Updates Service,
If it’s a group/channel, invokes the Channel/Broadcast Service to fan-out.
Recipients receive real-time updates via push (e.g., via Push Notification API or internal RPC).
5. Mobile App ↔ MTProto Gateway : Encrypted MTProto packets (message payload, auth token, seqno)
MTProto Gateway ↔ Auth & Session Service : Session ID, device info, user ID → returns session validity & permissions

## Deployment
1. https://raw.githubusercontent.com/mariachizhikova08/lab-01-market-product-and-git/main/docs/diagrams/out/telegram/deployment-diagram/Deployment%20Diagram.svg
2. https://raw.githubusercontent.com/mariachizhikova08/lab-01-market-product-and-git/main/docs/diagrams/src/telegram/deployment-diagram.puml?spm=a2ty_o01.29997173.0.0.2d4d5171K349pg&file=deployment-diagram.puml
3. Client apps run on user devices. MTProto Gateway and Bot API Server are deployed in geographically distributed edge data centers. Core services and data layers (Redis, sharded DB, DFS) run in regional clusters—often containerized—with Redis and DBs spanning availability zones. External integrations (SMS, push, bots) are third-party SaaS services.

## Assumptions
I assume the *Media & File Service* uses deduplication (e.g., content-based hashing) to store shared files once. I assume *Auth & Session Service* enforces strict session expiration and device binding for security.

## Open questions
"How are Secret Chats isolated from regular chats in the data layer—e.g., are they stored in a separate database cluster, encrypted end-to-end at rest, and how is key exchange coordinated between peers without involving core services?"
"What failure isolation mechanisms exist between the MTProto Gateway and Core Services—e.g., does the gateway implement circuit breaking or request throttling per user/device to prevent cascading failures during DDoS or client misbehavior?"