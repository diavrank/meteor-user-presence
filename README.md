# meteor-user-presence

Meteor user presence tracking helpers.

## Install

```sh
meteor npm install meteor-user-presence meteor-node-stubs
```

## Usage (server only)

```ts
import { UserPresence } from 'meteor-user-presence';

UserPresence.onCleanup(function onCleanup(sessionIds?: string[]) {
	if (!sessionIds) {
		Meteor.users.updateAsync({}, { $set: { 'status.online': false }, $unset: { 'status.idle': true } }, { multi: true });
	}
});

// When a user comes online we set their status to online and set the lastOnline field to the current time
UserPresence.onUserOnline(function onUserOnline(userId: string, connection?: Meteor.Connection) {
	if (connection) {
		Meteor.users.updateAsync(userId, {
			$set: {
				'status.online': true,
				'status.idle': false,
				'status.lastLogin.date': Utilities.currentLocalDate(),
				'status.lastLogin.ipAddress': connection.clientAddress,
				// @ts-ignore
				'status.lastLogin.userAgent': connection.httpHeaders['user-agent']
			}
		});
	}
});

// When a user goes idle we'll set their status to indicate this
UserPresence.onUserIdle(function onUserIdle(userId: string) {
	Meteor.users.updateAsync(userId, { $set: { 'status.idle': true } });
});

// When a user goes offline we'll unset their status field to indicate offline status
UserPresence.onUserOffline(function onUserOffline(userId: string) {
	Meteor.users.updateAsync(userId, { $set: { 'status.online': false }, $unset: { 'status.idle': true } });
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
