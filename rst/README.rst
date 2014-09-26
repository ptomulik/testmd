Resubmit
========

Simple Thunderbird add-on for massive re-submission of attached messages
forwarded to you by your users. This gathers and sends messages from multiple
senders to a single recipient (opposite to `Mail Merge`_).

Overwiev
--------

Having an (IMAP) folder, where I collect certain messages forwarded to me by my
users, the **Resubmit** tool extracts all attached messages (spam samples, for
example) from emails in the folder, for each extracted attachment it composes a
**new** message with the attachment attached to the **new** message and sends
it to the specified destination.

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

The extension defines three new filter actions, which you may use to configure
custom Thunderbird filters to resubmit spam samples. The filter actions are:

- **Resubmit - Send Now (Template)**,
- **Resubmit - Send Later (Template)**, and
- **Resubmit - Compose (Template)**.

Each action uses a template message which defines all the details of the
**new** message to be sent. Specifically, it contains sender and recipient
addresses, subject and content. The filter action just appends an attachment
(spam sample) to the template and submits it in this form. 

The **Resubmit - Send Now (Template)** action wraps an extracted attachment
with template message and sends it to a destination address(es) specified in
the template. The **Resubmit - Send Later (Template)** does the similar, but
queues the composed **new** message to be sent later. The **Resubmit - Send
Later (Template)** opens every **new** message in a compose window and lets you
inspect it, modify, send, save, queue or do with it whatever the compose window
allows to.


.. _Best Practices - Submitting spam samples to McAfee: https://community.mcafee.com/docs/DOC-1409
.. _Mail Merge: https://addons.mozilla.org/thunderbird/addon/mail-merge/ 
