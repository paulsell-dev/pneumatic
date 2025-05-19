# readme

Pneumatic is an sql client.

This is the parent, root, documentation, orchestration repo for the pneumatic project.

## System Design

Postman but SQL Client
Requirements
  Functional
    MVP
      Create a Query (file)
      Get Query(s)
      Run Query
      View Query Results
    Sharing
      Collection (folder) of queries (crud)
      Roles (owner, editor, viewer)
      Organization (a group of users which roles can be assigned to)
    QOL
      Variables (as ? marks)
      Result viewer optomizations
      AI Integrations
  Non Functional
    Availability over Consistance
    First Iteration is only for demo so 1 instance
    Needs to be able to transition to scalable model
    The query execution should not interact with our services.
      So, there isn't actually a lot of traffic for many users.
      IE, crud operations on files, folders, RBAC, and maybe connections.

Entities
  Users & Groups (Auth0)
  Files (query)
  Folder (& maybe a distinction between top level folder as 'collection')
    Sub folders
    Sub files
  Connection (locally only at first) ; sensitive
  Variables (locally only for sensitive ; sensitive

Routes
  MVP
    POST /files
    GET /files
    GET /files/:id
    PATCH /files/:id
  Sharing
    POST /collections
    GET /collections
    GET /collections/:id/collections
    GET /collections/:id/files
    Permissions / roles should be handled in auth0 as much as possible
  QOL

UI (Detour)

   ┌────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
   │                                                                                                                │
   │                                                                                                                │
   │  ┌──────────────────┐  ┌────────────────────────────────────────────────────────────┐  ┌────────────────────┐  │
   │  │ Folder & Files   │  │ ┌────────────────────────────────────────────────────────┐ │  │ Connections        │  │
   │  │                  │  │ │ Query Name & Params                                    │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ └────────────────────────────────────────────────────────┘ │  │                    │  │
   │  │                  │  │ ┌────────────────────────────────────────────────────────┐ │  │                    │  │
   │  │                  │  │ │ Query body                                             │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ │                                                        │ │  │                    │  │
   │  │                  │  │ └────────────────────────────────────────────────────────┘ │  │                    │  │
   │  │                  │  └────────────────────────────────────────────────────────────┘  │                    │  │
   │  │                  │                                                                  │                    │  │
   │  │                  │  ┌────────────────────────────────────────────────────────────┐  │                    │  │
   │  │                  │  │ Query Results                                              │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  │                  │  │                                                            │  │                    │  │
   │  └──────────────────┘  └────────────────────────────────────────────────────────────┘  └────────────────────┘  │
   │                                                                                                                │
   └────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘


High level

    ┌─────────────┐
    │ Blob Storage│
    │             │
    │             │
    │             │
    └─┬───────────┘
     ▲│ Pre Signed Urls
     ││
     ││
     │▼             Token
    ┌┴────────────┐ Files           ┌──────────────────┐          ┌───────────────┐
    │ Client      │ Folders         │ Services         │ Files    │ DB Postgres   │
    │             │ ~               │                  │ Folder   │               │
    │             │                 │                  │          │               │
    │             │ ◄────────────── │                  │ ───────► │               │
    │             │ ──────────────► │                  │ ◄─────── │               │
    └─────────┬───┘                 │                  │          │               │
      ▲       │ ▲ Query &           └──────────────────┘          └───────────────┘
Token │       │ │ Response
      │       ▼ │
┌─────┴──┐  ┌───┴──────┐
│ Auth0  │  │ DB       │
│        │  │          │
│        │  │          │
└────────┘  └──────────┘