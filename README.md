# meteor-user-presence

Meteor user presence tracking helpers.

## Install

```sh
meteor npm install meteor-user-presence meteor-node-stubs
```

## Usage (server only)

```ts
import { UserPresence } from 'meteor-user-presence';

UserPresence.onUserOnline((userId) => {
  // handle user online
});

UserPresence.onUserIdle((userId) => {
  // handle user idle
});

UserPresence.onUserOffline((userId) => {
  // handle user offline
});

UserPresence.onCleanup((sessionIds) => {
  // handle cleanup of stale sessions
});
```

## Client status updates

By default, sessions are marked online on login and removed on logout or disconnect.
If your app needs explicit idle/online updates, call the built-in method:

```ts
Meteor.call('updateSessionStatus', 'idle'); // or 'online'
```

`updateSessionStatus` accepts `online`/`idle` (or `2`/`1`) and updates the
current connection session.

## Notes

- Requires Meteor server context (imports should run on the server).
- Presence data is stored in `presence:servers` and `presence:user-sessions`.
- A watcher server cleans up stale sessions (heartbeat ~5s, TTL ~10s).

## License

MIT
