# Linux Ops Arena - Participant Guide

Welcome to Linux Ops Arena, a competitive Linux system administration event. This guide covers what the event is, what you need to bring, how the setup works, and what to study so you walk in ready. It does not list the actual tasks or faults, so treat it as a preparation map rather than an answer key.

## What the event is

You compete as a team, but each member works on their own virtual machine that we provide as a downloadable image. Your VM runs a small agent that checks your work continuously and reports your progress to a live scoreboard. There is nothing to submit by hand. When you fix or build something correctly, your score moves within a few seconds.

The event runs in two rounds:

- Round 1 (Phase 1) is a build round. You start from a clean, working Linux VM and set up a set of services and configurations from scratch.
- Round 2 (Phase 2) is a troubleshooting round. You receive a VM that already has a number of things broken across the system, and your job is to find and fix them.

All teams from Round 1 advance to Round 2, use Round 1 which is easier to get more points so you can place in the TOP 10 teams. Scoring rewards both correctness and speed, so getting something working sooner is worth more than getting it working late.

## What to expect

- The work is hands-on at the Linux command line. There is no graphical hand-holding, and you are expected to use standard tools that are already on the VM.
- You do not need to write large programs. You need to configure, fix, and verify a working system the way an administrator would.
- The scoreboard is live. You can watch your team climb, see how many items you have resolved, and, in Round 2, see which broad areas still need attention.
- You will not be told exactly what to do or exactly what is broken. You read the system, follow the signals it gives you, and confirm your fixes.

## Prerequisites

Please have these ready before the event, because setup time on the day is limited.

- A laptop that can run a virtual machine. VMware Workstation (the free Player edition is fine) or an equivalent that can import a standard OVA file.
- Enough free resources for one VM. Plan for about 2 CPU cores, 4 GB of RAM, and 20 GB of free disk space.
- A working internet connection. The VM only needs outbound access to reach the scoreboard, and the default NAT networking mode handles this. You do not need to open any ports or change firewall settings on your own machine.
- Basic comfort with the Linux command line. You should be able to move around the filesystem, edit files, read logs, and manage services without a cheat sheet for every step.
- Your team credentials. Each member receives an email and password from the organizers before the event. Keep them handy.

## How the setup works on the day

1. Import the VM image we provide into your virtualization software.
2. Leave the network adapter on NAT and make sure the VM has internet access.
3. Boot the VM and log in with the account details we give you.
4. Run the registration command, enter your email and password, and your VM begins reporting to the scoreboard.

```
sudo register
```

After that, you simply do the work. The agent checks your VM on a short interval and updates your team score automatically. If you break something you had already fixed, the score reflects that too, so verify your changes hold.

## How to prepare for Round 1 (build)

Round 1 is about setting up common services cleanly and correctly. You do not need memorized answers, but you should be fluent in the everyday tasks below. If any of these feel unfamiliar, practice them on a spare Linux VM beforehand.

- Installing, enabling, and starting services with the package manager and systemctl.
- Basic web server setup: installing a web server, serving a page, and configuring more than one site or port.
- Users and groups: creating them, managing group membership, and setting file ownership and permissions correctly.

- Database basics: installing a database server, securing it, and creating a database and a user with the right privileges.
- systemd beyond the basics: creating a service override and setting environment values for a service.
- Reading system logs to confirm what a service is doing.
- Scheduling a recurring job.
- Writing a short shell script that checks something and exits with a success or failure status.

A good way to prepare is to install a clean copy of a Red Hat family Linux (such as Rocky Linux 9) and rehearse each of these until they are second nature.

## How to prepare for Round 2 (troubleshoot)

Round 2 gives you a system where several things are broken at once, spread across different areas. You are not told what they are. The scoreboard will show you which broad areas still have unresolved issues, which points you toward where to look without telling you the fix.

The areas in play include web services, databases, networking, storage, background services, scheduled jobs, logging, user access, system time, software repositories, and process limits. Be ready to work in any of them.

The most useful preparation is method, not memorization. Practice a calm, systematic approach:

- Check the state of services and read their logs to see why they are unhappy.
- Confirm what is listening on the network and what is not.
- Look at permissions and, importantly, SELinux, which is enforcing in this round. File contexts and denials matter, so know how to check and correct them.
- Inspect the firewall, disks and mounts, name resolution, and software repositories.
- Fix one thing at a time and verify each fix before moving on.

Some issues may sit on top of others, so resolving one can make the next one easier to see. Trust the signals the system gives you and work through them in order.

## A few ground rules

- The scoring agent is off limits. Do not try to tamper with it, feed it false information, or interfere with how it reports. Final standings are verified, and tampering is easy to spot.
- Keep the VM on NAT networking and do not change the agent configuration. The agent needs its connection to the scoreboard to score your work.
- You do not submit anything manually. Just do the work and let the scoreboard update.
- Coordinate as a team. You each have your own VM, so divide the work and compare notes.

## Before you arrive, checklist

- Virtualization software installed and able to import an OVA.
- Room for a VM with roughly 2 CPU, 4 GB RAM, and 20 GB free disk.
- A reliable internet connection.
- A practice run on a clean Rocky Linux 9 VM covering the Round 1 skills above.
- Your team credentials saved somewhere you can find them quickly.

Event details such as the exact schedule, venue, and how the VM image and credentials will be distributed will be shared separately on Discord. If anything here is unclear, ask in the event channel before the day so you are not troubleshooting your own setup during the competition.

