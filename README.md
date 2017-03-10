# Automatic daily reports of used applications with Thyme.

These scripts allow you to get daily automatic reports of your complete graphical activity on applications.

## :fire: *Limitations* :fire:

There are number of limitations :
* I work on Debian, so I did **not** test these scripts on another distributions.
* Some names directories are hard coded in the code ; you can only choose the base directory or edit the code.
* I use the both of cron and anacron.
* I did **not** even looked how thyme works, so perhaps a better way exists ? :thumbsdown:
* And so on...

## Thyme ?

[Thyme](https://github.com/sourcegraph/thyme) is a portable application, written in Go, which is able to capture all windows open on your computer, and writes it down on a JSON-formatted file. By multiple and regular calls, the JSON file grows up, so you obtain a very detailed timeline of the time interval you called regularly Thyme.
You can prove to your boss that you have spent ~~only 37% of your day on facebook~~ 63% of your day on your IDE ! :clap:

## Organization of the application.

### Thyme operation

Thyme writes the applications only once per call. So, we have to set a timer to call it regularly. To generate a report, we have to call it once, but with different parameters. We have also to set another timer, which triggers thyme every 24h. Therefore, a laptop is not powered on 24h a day, we have to use anacron to be able to generate the reports when the computer is powered after the set hour of generation.

Also, we have to store the JSON file which stores the continuously captured opened applications.

Finally, it is interesting to have a direct access to the current daily report, as well as have an access to older reports.

Considering the facts, I decided to organize my folders as :

```
.
└── thyme
    ├── live_capture
    └── reports
        ├── backups
        └── today
```

The root directory must be recursively writeable.

## How to make it working on my Debian computer ?

First, we create a directory to store the script which call thyme every 15 seconds and we place the script into :

```bash
$ sudo mkdir -p /etc/cron.15s
$ sudo cp <scripts_directory>/thyme.track /etc/cron.15s
$ sudo chmod 755 /etc/cron.15s/thyme.track
```

Then we create the cron rule to execute the script every minute :

```bash
$ crontab -e
```

And we add as a new rule :

```bash
* * * * * /etc/cron.15s/thyme.track -p <application_root_directory> # Folders will be created under this root
```

We place the second script on its place : 

```bash
$ sudo cp <scripts_directory>/thyme.stats <scripts_directory>/thyme_laucher /etc/cron.daily
```

:fire: ***WARNING*** :fire:

anacron have *very* precise rules to execute a script placed on ```/etc/cron.daily``` and others ; particularly, the script **MUST** ***NOT*** have an extension (exactly, no point in the name, but...). So, the script ```/etc/cron.daily/thyme_launcher``` calls the real script with its needed arguments. Plear refer to usage function of thyme.stats to use.
