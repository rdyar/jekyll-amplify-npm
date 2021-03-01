# jekyll-amplify-npm
Starter for using Amplify on a jekyll site with NPM scripts handling sass/js and images

Uses NPM scripts to compile sass, uglify js and move images from src to _site. These files are not processed by Jekyll.

## Why not have Jekyll process everything?

- sass sourcemaps
- uglify js
- minify html
- reduce load on files Jekyll has to process - make the core things we need Jekyll to do faster
- npm watch script can watch _config file for changes and rebuild

## Why use AWS Amplify? 

- allows any plugins
- simple continuous deployment
- dev and production sites
    - password protected dev site
- custom headers
- control over redirects
- dirt cheap (for sites like mine at least)

## Things to not like:

- it is a little complicated at first
- several dependencies
- no access to actually see the hosted files, kind of a mystery what happens after the site is deployed. But this is no different than GH pages.

## How it works

First you need to install all the package.json stuff - this will require Node. Run NPM install (or npm i).

All of your static assets - sass, js and images go into a folder named _assets. Jekyll will ignore this file as it begins with an underscore. These files are handled by the NPM scripts. In the Jekyll config file it is important to set `keep_files: [assets]` so that Jekyll does not delete them every time it rebuilds. As it is now the NPM script looks for a sass folder, a js folder and an images folder and processes and watches those files only.  

Check what is in the package.json file under scripts. The only ones I call directly at the moment are:

- npm run dev - launches a server, watches all the files in the source and rebuilds jekyll/sass/js or moves images/files when it sees changes
- npm run deploy - this would be run from amplify. Runs all the build steps, then minifies html and css.

## Things to watch out for

I currently don't have the jekyll version pegged to anything, and in Amplify I tell it to use the most recent version. This could get out of whack as Amplify changes its base image. For instance initially their default Ruby version was 2.3 which was not compatible with Jekyll 4 so it didn't work and I needed to have an rvm command to tell it to use 2.6.x. This has changed in the few months I have been doing this. Now 2.4 is the default Ruby version so I commented out the rvm line in the build file.

On the Amplify console you can edit the build file. I don't use that, the build file is here in the repo that way it is easier to know how I have it setup and I can use it in other projects easier. Same goes for the custom headers file. There is a way to edit that on the Amplify console but it is overwritten if the file exists in the root which is what I have.

## Can I use it without Amplify?

Sure, there isn't really anything in here that is special as far as the hosting is concerned other than the amplify and customHttp files. So you can delete those and just use the NPM scripts and host how ever you want.

# Careful with the Custom Http file! 

Don't use this file if you don't understand what it does. It would be very easy to change the cache time to live that the browser uses and make it so your users never see new changes you make!
