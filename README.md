
Getting Started with Slate
------------------------------

### Prerequisites

You're going to need:

 - **Linux or macOS** — Windows may work, but is unsupported.
 - **Ruby, version 2.3.1 or newer**
 - **Bundler** — If Ruby is already installed, but the `bundle` command doesn't work, just run `gem install bundler` in a terminal.


Likely you want to install Ruby via RVM: https://rvm.io/


### Getting Set Up

Initialize and start Slate. You can either do this locally, or with Vagrant:

```shell
# either run this to run locally
bundle install
bundle exec middleman server

# OR run this to run with vagrant
vagrant up
```

You can now see the docs at http://localhost:4567


### Build and publish 

#### Static
You can publish static documents to your own server using 

```shell
bundle exec middleman build --clean
```

Middleman will build your website to the build directory of your project, and you can copy those _static_ HTML files 
to the server of your choice.

#### Github pages 

Run `./deploy.sh` to publish to github pages. WARNING: anything published to github pages is PUBLIC. 


Learn more about [editing Slate markdown](https://github.com/lord/slate/wiki/Markdown-Syntax), 
or [how to publish your docs](https://github.com/lord/slate/wiki/Deploying-Slate).

If you'd prefer to use Docker, instructions are available [in the wiki](https://github.com/lord/slate/wiki/Docker).

### Note on JavaScript Runtime

For those who don't have JavaScript runtime or are experiencing JavaScript runtime issues with ExecJS, it is recommended 
to add the [rubyracer gem](https://github.com/cowboyd/therubyracer) to your gemfile and run `bundle` again.
