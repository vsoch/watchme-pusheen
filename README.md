# Price Watcher

This is an example watcher to use [urls tasks](https://vsoch.github.io/watchme/watchers/urls) 
to monitor changes in prices over time. Per the preferences of @vsoch, we are monitoring the change in prices of
different Pusheens. Specifically, we will create tasks to get a price (in dollars)
for pusheen from Amazon, Ebay, and the Pusheen main site. Each has a task folder created:

 - [task-amazon](task-amazon)
 - [task-ebay](task-ebay)
 - [task-pusheencom](task-pusheencom)


### What is WatchMe?

WatchMe is a [tool for reproducible monitoring](https://vsoch.github.io/watchme).
This means that you can create one or more tasks to monitor web or system resources,
and collection version controlled results (git) at a regular interval (cron). 
For more details, see the [documentation base](https://vsoch.github.io/watchme)


## Clone

To clone this repository (and start with the watchers here), you can
first install watchme:

```bash
$ pip install watchme
```

and then get the repository:

```bash
              # repository                                # watcher name
$ watchme get https://www.github.com/vsoch/watchme-pusheen pusheen
Added watcher watchme-system
```

Conflrm that it was added:

```bash
$ watchme list
pusheen
system
```

This will install the data to your $HOME/.watchme folder by default, unless
you've exported another `WATCHME_BASE_DIR`. Before you run the tasks, 
take a look at the data that has already been collected. For eack task
folder, you can export changes for a file like this 

```bash
#              <watcher> <task>          <filename>
$ watchme export pusheen task-pusheencom result.json
```

If you expect the data in the files to be json (and want to parse it into the result)
then do this:

```bash
$ watchme export pusheen task-pusheencom result.json --json
```

There is a `TIMESTAMP` file that is kept in each folder as a record of when 
it was last run. You can then run the task manually in test mode to see output

```bash
$ watchme run pusheen --test
```

But likely you want to activate and schedule the task to run.


### Schedule the Task

Instead of a manual run, you likely want to run the task and look for changes 
over time. You can do that like this:

```bash
$ watchme schedule pusheen @daily
```

And then check that an entry has been added to crontab:

```bash
$ crontab -l
@daily /home/vanessa/anaconda3/bin/watchme run pusheen # watchme-pusheen
```

Finally, ensure that the watcher is active:

```bash
$ watchme activate pusheen
```

## Creation

If you want to reproduce creating this watcher (instead of cloning the repository), 
it looks something like this:

Install Watchme

```bash
$ pip install watchme
```

Initialize the base folder at $HOME/.watchme

```bash
$ watchme init
```

Create the pusheen watcher:

```bash
$ watchme create pusheen
Adding watcher /home/vanessa/.watchme/pusheen...
Generating watcher config /home/vanessa/.watchme/pusheen/watchme.cfg
```

And then install each of the tasks as follows:

```bash
$ watchme add pusheen task-pusheencom url@https://shop.pusheen.com/collections/pusheen type@urls func@get_url_selection get_text@true selection@.money url_param_page@1,2,3,4,5,6,7 save_as@json
$ watchme add pusheen task-amazon url@https://www.amazon.com/GUND-Pusheen-Stuffed-Animal-Plush/dp/1223104788/ selection@#price_inside_buybox get_text@true type@urls func@get_url_selection
$ watchme add pusheen task-ebay url@https://www.ebay.com/itm/GUND-Pusheen-Pusheenosaurus-Dinosaur-Plush-Green-6/133014227523 selection@#prcIsum get_text@true type@urls func@get_url_selection
```

After the original creation, you can easily export a current configuration file like this:


```bash
$ watchme inspect pusheen --add-command
```

## Export Data

As an example, I created this watcher with the commands above, and 
decided to export data (after about a day and a half) and do an analysis to
show change in metrics about my computer over time. This simple example will serve
to show that WatchMe is useful to answer research questions, as it collected my data
for me without me needing to do anything. Here are the commands to export each of the data
files:

```bash
mkdir -p data
watchme export pusheen task-amazon --out data/task-amazon.json result-0.txt
watchme export pusheen task-pusheencom --out data/task-pusheencom.json result.json
watchme export pusheen task-ebay --out data/task-ebay.json result-0.txt
```
