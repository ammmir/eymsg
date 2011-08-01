# Unnamed Messaging API

The messaging API allows you to integrate one-to-one and one-to-many group
messaging into your application with little development effort. It also
gives email-based commenting (like Facebook Comments) for free.

## Accessing the API

The base API adheres to REST principles and accepts and returns JSON.
Most of the messaging functionality can also be accessed by including
a JavaScript snippet on any page.

## Mapping User Accounts

The API has no knowledge of your users nor does it need to. However, to
enable sending comment notifications via email and to allow replies, you
will need to supply the following data for users:

    * user id
    * user display name
    * email address

Note that `user id` is specific to your app. It is recommened that you use
your internal database id or other primary key for the user. The API treats
`user id`s as unique per application.

### Listing Users

    GET /users

Returns:

    {
      count: 2,
      users: ["1", "2"]
    }

### Creating/Updating a User

    PUT /users/1
    {
      name: "Amir",
      email: "amir@example.com"
    }

### Getting a User

    GET /users/1

Returns:

    {
      name: "Amir",
      email: "amir@example.com"
    }

### Removing a User

    DELETE /users/1

## Creating/Modifying a Topic

You don't explicitly need to create a topic, but creating one allows you
to customize the defaults:

    PUT /topics/foo
    {
        email_notifications: true,
        email_replies: true
    }

The above will enable email notifications and replies. If you try to post
a message into this topic and a `user id` doesn't have an email address
associated with it, the request will fail.

## Listing Topics

A topic is a list of messages identified by a key. The key can be any
string, and is often an id or a URL. Namespacing topic keys is up to
the application.

    GET /topics

Returns:

    {
      count: 1,
      topics: [
        {id: "foo", create_date: "...", update_date: "...", participants: 2}
      ]
    }

## Listing Messages in a Topic

    GET /topics/foo

Returns:

    {
      create_date: "...",
      update_date: "...",
      participants: 2,
      count: 2,
      messages: [
        {id: "m1", from: 1, date: "...", text: "test message."},
        {id: "m2", from: 2, date: "...", text: "worked!", in_reply_to: "m1"}
      ]
    }

## Listing Message Details

    GET /topics/foo/m1

Returns:

    {
      from: 1,
      date: "...",
      text: "test message."
    }

## Removing a Message

    DELETE /topics/foo/m1

## Posting a Message

You may post a message into any topic. If the topic id doesn't exist, a
new one will be created with defaults taken from your account.

    POST /topics/foo
    {
      from: 1,
      text: "why was my message removed?"
    }

## Checking if a Topic Exists

    HEAD /topics/foo

## Checking if a Message Exists

    HEAD /topics/foo/m1

## HTTP Response Codes

The API will emit standard HTTP response codes in REST fashion.

## REST Fallback for Legacy Clients

To accommodate older HTTP clients that are only able to make GET and POST
requests, include either an `X-HTTP-Method-Override` HTTP header or a
`_method` query string parameter specifying the actual method to be used.

In the latter case, a DELETE request would look like:

    GET /topics/foo/m1?_method=DELETE

And a PUT request would look like:

    POST /topics/foo?_method=PUT
    {
      ...
    }
