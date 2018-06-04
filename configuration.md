# Configuration

## Settings

### Basics

#### Default time allowance per day

This setting determines the total number of minutes a user will have for the day by default.

{% hint style="info" %}
The value of **Default time allowance per day** should be a whole number.
{% endhint %}

#### Default time allowance per session

This setting determines the total number of minutes a user will have for each _session_ per day. So, if a system is configured with 120 minutes time allowance per day, and 30 minutes time allowance per sessions, a user will be able to log in 4 times per day for 30 minutes at a time.

{% hint style="info" %}
The value of **Default time allowance per session** should be a whole number.
{% endhint %}

#### Default guest time allowance per session

This is the same as **Default time allowance per session**, except for guest accounts instead of regular uses.

{% hint style="info" %}
The value of **Default guest time allowance per session** should be a whole number.
{% endhint %}

#### Client registration update delay limit

When a Libki client launches, it periodically connects to the Libki server to register itself. If a client does not re-register itself within this number of minutes, the Libki server will remove it from the list of active clients.

{% hint style="info" %}
The value of **Client registration update delay limit** should be a whole number.
{% endhint %}

### Data retention

#### History retention

This setting controls how long user and client data is retained for the purpose of generating statistics and checking usage history, in days. If this setting is left empty, the data will be kept indefinitely.

{% hint style="info" %}
The value of **History retention** should a whole number.
{% endhint %}

#### Inactive user retention

This setting controls how long a user can be inactive before being automatically deleted, in days. If this setting is left empty, all users will be kept indefinitely.

{% hint style="info" %}
The value of **Inactive user retention** should be a whole number.

This setting should be used to conform to [GDPR](https://www.eugdpr.org/) if necessary.
{% endhint %}

### Client behavior

This setting controls how clients may be used by users. There are three possibilities.

* First come, first served.
  * Allows a patron to log in to any client not currently being used.
* Reservation only.
  * Requires a patron to place a reservation for a client before logging in to it.
* First come first served, with option to place reservation.
  * A hybrid approach that allows patrons to log in to any client that is not currently in use or reserved.

{% hint style="info" %}
The setting Display usernames controls if the user's username is displayed on the reserved client computer.
{% endhint %}

### Reservations

#### Reservation timeout

The amount of time \( in minutes \) that a user has to log into his or her reserved computer. If the user does not log in within the specified time limit, the reserved client will become available again.

{% hint style="info" %}
The value of **Reservation timeout** should be a whole number.
{% endhint %}

#### Display usernames

This setting determines if a Libki client that is reserved and waiting will display the username of the person it is waiting for.

### Automatic time extension

Libki has an automatic time extension system that allows users to remain logged into a client computer beyond the pre-determined number of minutes per session a user is allotted.

#### Extension length

The amount of time to automatically increase a user's session time by \( in minutes \).

{% hint style="info" %}
The value of **Extension length** should be a whole number.
{% endhint %}

#### Extend time at

This setting controls at what point in time an extension length is triggered. A session will be extended when the user's session time drops below this number, in minutes.

{% hint style="info" %}
The value of **Extend time at** should be a whole number.
{% endhint %}

#### Extend time unless

## Closing Hours

## Single Sign-on

### SIP2

### LDAP

## Print Management



