s3wipe
======

A rapid parallelized AWS S3 key &amp; bucket deleter.

I was recently tasked with deleting an bucket in Amazon's Simple Storage
Service (S3) that contained an absolutely massive number of files.

Unfortunately, Amazon themselves do not give you an easy way to do this
yourself.  Their web interface stalls indefinitely when you delete an
"adequately large" number of files, and their CLI tool (aptly named
"aws-cli") only deletes files in a single-threaded fashion (i.e. slowly).

After googling around a bit, I encountered
[s3nukem](https://github.com/lathanh/s3nukem) (itself a fork
of [s3nuke](http://github.com/SFEley/s3nuke/)), which appeared to be the
solution to my problems.  After a few minutes of trying to find the
'right' version of RightAWS (the s3nukem code & readme had a
disagreement over this), I was able to get it up and running.  However,
after a bit of back-of-the-napkin math, it was looking like it was still
going to take at least a month of running s3nukem before the bucket was
deleted.

So, I wrote s3wipe.  S3wipe, as far as I know, is the only S3 key/bucket
deletion tool that:

* Does parallel, thread-based delete AND list operations (more speed)
* Performs batch deletes (MOAR SPEED!)
* Will delete versioned objects (MOAR... well, deletes)

Using s3wipe, I was able to delete _400 million S3 objects in about 24 hours_.

## Installation

This is just a single-file script, so just go ahead and run it.  It will need
a semi-recent version of the "boto3" python module to be installed, though, so:

    pip install boto3

Then:

    wget https://raw.github.com/gbonk/s3wipe/master/
    chmod 755 s3wipe

### Using Docker (TODO)

Clone the repo:

    git clone git@github.com:gbonk/s3wipe.git
    cd s3wipe

TODO Build the Docker image:

    docker build -t s3wipe:latest .

TODO Then run the script:

    docker run s3wipe:latest --help

## Usage

```
usage: s3wipe [-h] --path PATH [--id ID] [--key KEY] [--token TOKEN] [--profile PROFILE]
              [--batchsize BATCHSIZE] [--maxqueue MAXQUEUE] [--delbucket] [--dryrun] [--quiet]

Recursively delete all keys in an S3 path

optional arguments:
  -h, --help             show this help message and exit
  --path PATH            S3 path to delete (e.g. bucket/path)
  --id ID                Your AWS access key ID (Optional)
  --key KEY              Your AWS secret access key (Optional)
  --token TOKEN          Your AWS session token. Typical in MFA (Optional)
  --profile PROFILE      The AWS Profile to use (Optional)
  --region REGION        The Region to use (Optional)
  --dryrun               Don't delete. Print what we would have deleted
  --quiet                Suppress all non-error output
  --batchsize BATCHSIZE  # of keys to batch delete (default 100)
  --maxqueue MAXQUEUE    Max size of deletion queue (default 10k)
  --delbucket            If S3 path is a bucket path, delete the bucket also
```

## Changelog

_v0.0.2_

    Added the additional options for connecting via region, using keys, etc and moved the code into a factory

_v0.0.1_

    Now uses Boto3 and your default credentials in ~/.aws/credentials
    A lot of new options to implement

_v0.2_

    You can now delete all keys under an arbitrary S3 path, instead of only
    entire buckets (although that is still an option, as well).

_v0.1_

    Initial version.
