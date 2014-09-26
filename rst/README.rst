Resubmit
========

Simple Thunderbird add-on for massive re-submission of attached messages
forwarded to you by your users. This gathers and sends messages from multiple
senders to a single recipient (opposite to `Mail Merge`_).

Overwiev
--------

Having an (IMAP) folder, where I collect certain messages forwarded to me by my
users, the **Resubmit** tool unpacks all attached messages (spam samples, for
example) from emails in the folder, for each spam sample composes a **New**
message with the spam sample attached to it and sends it to the specified
destination.

Motivation
----------

I found this extension useful in the following scenario. My users send me spam
samples and I have to resubmit it to McAfee. According to their article
`Best Practices - Submitting spam samples to McAfee`_, the samples should be
included as attachments into **new** messages. The emails with samples should
originate from a customer address (e.g. *admin@example.com*) known to McAfee.

So, this is basically job for a tool such as **Resubmit** add-on.

More details
------------

The extension adds three filter actions, which you may use to configure custom
Thunderbird filters to resubmit spam samples. The filter actions are:

- **Resubmit - Send Now (Template)**,
- **Resubmit - Send Later (Template)**, and
- **Resubmit - Compose (Template)**.

Each action uses a template message. The template message defines all details
of the wrapping message to be submitted to the spam centre. Specifically, the
template contains sender and recipiend addresses, subject and content. The
**Resubmit**'s filter action then just appends an attachment (spam sample) to
the template and submits it with the sample attached.


.. _Best Practices - Submitting spam samples to McAfee: https://community.mcafee.com/docs/DOC-1409
.. _Mail Merge: https://addons.mozilla.org/thunderbird/addon/mail-merge/ 
