hackathon 2016
==============

Welcome to hackathon 2016!

Download the kickoff deck for more information on the business problem, the hackathon agenda, the data, and more! Find it in the data folder:

[Hackathon 2016 kickoff deck](https://github.com/tresata/hackathon2016/blob/master/data/hackathon_kickoff_deck2016.pdf)

## Machines

We have a Hadoop cluster with one master and four slaves. The slaves have sixteen cores, 15 1TB data drives, and 128GB of RAM. You will have ssh access to the slaves.

Please spread yourselves out across the machines.

## Getting Started

You can obtain a username and login information from one of the Tresata representatives.

Edit your hosts file and add a line to associate the ip to the hackathon hostname:

[Here](https://support.rackspace.com/how-to/modify-your-hosts-file/) is the helpful link for editing your hosts file for Windows, Mac and Linux

Ssh into a server where you can access the retail data stored on the hackathon Hadoop cluster.

    > ssh <username>@<ip-address>

and enter the password you were given.

We made Hive, Spark, and pySpark command-line interfaces available, and included a tool to compile and run simple Scalding scripts on-the-fly.
