# lynofx #
### Command-line client for OFX requests ("Lynx for OFX") ###

The `lynofx.py` script is a command-line OFX client -- the name is meant to 
suggest "[Lynx](http://lynx.browser.org/) for OFX."  This can be very useful when 
testing out OFX connections or debugging a connection to a particular server,
or just for getting your data out of your bank on the command line.

## Installation ##

Python 2.6 or above is required. The project has not been tested at all with Python 3.

The `lynofx` project depends on another Wesabe repo, `fixofx`.
`fixofx` is a submodule of the lynofx project as a result.

First, clone the repo from GitHub:

    git clone git://github.com/wesabe/lynofx.git

Then, initial the submodule:

    cd lynofx
    git submodule init
    git submodule update

With that you should be good to go.

## Options ##

`lynofx.py` requires a number of options to work.  Some of the options ask for
account information, but many ask for OFX parameters that are required for the
request.  Unfortunately the OFX parameters for different financial institutions
are not well known, and they are not included with this script.  The three values
needed are the OFX URL, OFX FID, and OFX ORG.  One place to look for these values
is at [the GNUCash wiki](http://wiki.gnucash.org/wiki/OFX_Direct_Connect_Bank_Settings)
or just searching on Google.  If neither of those work, try calling your financial 
institution and asking them; larger credit unions, especially, are sometimes 
willing to help.

Invoke `lynofx.py` with the options for your institution, any other options
needed, and the action you want to perform.  Three actions are supported:

* `profile` -- This returns a profile of the institution.  Generally, you don't
  need this profile for anything, but if you were building an OFX server
  crawler, the data in the profile would be useful.  Also, since you don't
  need a username or password for the institution to get the profile (usually),
  the `profile` action is a good "ping" test to see if the server is up and
  considers your request well-formed. The `profile` action requires the `-f`, 
  `-o`, and `-u` options.
* `accounts` -- This returns a list of the accounts this institution has for 
  a given username and password.  This is very useful, since asking for a
  statement from an institution requires a lot of the information in the account
  information list, and sometimes helps show you the expected format for those
  values when you're making statement requests.  (For instance, USAA account
  numbers are eight digits, but their OFX server expects a ten-digit account
  number, zero-padded, such as 0012345678.  Since the account info response 
  shows the format exactly as they expect it, you can avoid errors by using this
  value instead of entering the number any other way.)  Specifically, the `BANKID`, 
  `ACCTID`, and `ACCTTYPE` fields in the account info list are needed for the
  `statement` action, below.  The `accounts` action requires the `-f`, `-o`, and 
  `-u` options, and a username and password for the institution.
* `statement` -- This returns a statement for the account requested, including
  current balances and a list of transactions.  See the `accounts` action, above,
  to learn the required option values for this action.  The `statement` action 
  requires the `-f`, `-o`, `-u`, `-b`, `-i`, and `-t` options, and a username 
  and password for the institution.

Use the `lynofx.py` script like this:

    ./lynofx.py [options] (profile|accounts|statement)

The command-line options supported by `lynofx.py` are as follows:

    --version                          show program's version number and exit
    -h, --help                         show this help message and exit
    -v, --verbose                      be more talkative, social, outgoing
    -r, --raw                          show raw response (don't pretty-print as OFX/2.0)
    -f FID, --fid=FID                  OFX ID of the financial institution
    -o ORG, --org=ORG                  OFX organization of the financial institution
    -u URL, --url=URL                  OFX URL of the financial institution
    -t ACCTTYPE, --accttype=ACCTTYPE   type of the account
    -i ACCTID, --acctid=ACCTID         ID of the account (a.k.a. account number)
    -b BANKID, --bankid=BANKID         ID of the bank (a.k.a. routing number)

The script will prompt for username and password, if required, after it is run.

## Contributing ##

Contributions to lynofx are welcome. Please add tests for your contribution
and make sure all tests pass before sending a pull request. Here are some
ideas for things to do:

* Allow the user to specify a username and password from the command line or a 
  config file so the script can be run out of cron.
* Allow the user to configure the user-agent string and the client identifier claimed.
* Add options for specifying the date range of the request.
