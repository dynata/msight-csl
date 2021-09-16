:orphan:

Accounts correspond to business entities that have some sort of relationship,
either directly or indirectly, with the MarketSight platform.
Think of Accounts as "companies".

Accounts may contain :class:`Users`, :class:`Datasets`, :class:`Crosstabs`,
:class:`Charts`, etc.

Each Account has a primary contact. This is the main point of contact for the
Account, and the individual who is directly responsible for everything related
to that Account.

Accounts may also have multiple administrators, who are allowed to perform more
actions than a typical :class:`User <Users>`, but not as many actions as the
primary contact.

Accounts may be registered as a fully-paid Account, or as a Trial Account. Trial
Accounts will expire after a certain amount of time, will have a different
experience within the MarketSight platform, and will receive periodic
notifications about their trial status encouraging them to sign up for a paid
Account.