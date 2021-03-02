# jekyll-amplify-npm
Starter for using Amplify on a jekyll site with NPM scripts handling sass/js and images

Uses NPM scripts to compile sass, uglify js and move images from src to _site. These files are not processed by Jekyll.

# Careful with the customHttp.yml file! 

Don't use this file if you don't understand what it does (delete it). It would be very easy to change the cache time to live that the browser uses and make it so your users never see new changes you make! it is possible I have it set in such a way that it works great for me, but your files may get messed up. For instance your html files could be named `.htm` instead of `.html` and that would be really bad. I have it set to cache anything that is not `.html` for at least 3 days (.css and .js) and much longer for everything else. `.html` files are cached for 10 minutes.

## Why not have Jekyll process everything?

- sass sourcemaps
- uglify js
- minify html
- reduce load on files Jekyll has to process - make the core things we need Jekyll to do faster
- npm watch script can watch _config file for changes and rebuild
- htmlhint checker

## Why use AWS Amplify? 

- allows any plugins
- simple continuous deployment
- dev and production sites
    - optional password protected dev site (or main site)
- custom headers
- control over redirects
- dirt cheap (for sites like mine at least)

## Things to not like:

- it is a little complicated at first
- several dependencies
- no access to actually see the hosted files, kind of a mystery what happens after the site is deployed. But this is no different than GH pages.

## How it works

First you need to install all the package.json stuff - this will require Node. Run NPM install (or npm i).

All of your static assets - sass, js and images go into a folder named `_assets`. Jekyll will ignore this file as it begins with an underscore. These files are handled by the NPM scripts. In the Jekyll config file it is important to set `keep_files: [assets]` so that Jekyll does not delete them every time it rebuilds. As it is now the NPM script looks for a sass folder, a js folder and an images folder and processes and watches those files only. If your folders are not named like that, change the npm script rather than the folder names or else you will need to update all the references.  

Example:  
The script is looking for images in `_assets/images` - if your folder name is different you should probably just update the line in the script that moves the images. This would be a lot easier than updating every image reference. The line in question is:  
"move-images": "copyfiles -a -V -u 1 \"_assets/images/**/*\" _site/assets",

In the middle where it says _assets/images - you can change that to img or pics or whatever. Still should be in the _assets folder though. When the site is built it will turn into `assets` - without the underscore.

Check what is in the package.json file under scripts. The only ones I call directly at the moment are:

- npm run dev - launches a server, watches all the files in the source and rebuilds jekyll/sass/js or moves images/files when it sees changes
- npm run deploy - this would be run from amplify. Runs all the build steps, then minifies html and css.

On the build step that actually runs `jekyll build` I also have it run `htmlhint`. This should work even if htmlhint finds an error as other scripts run after it. I like this so that I can see the errors in the terminal and fix them while the dev server is still running and the files are being watched for changes. You can also see the results in the build logs on Amplify.

## Things to watch out for

I currently don't have the jekyll version pegged to anything, and in Amplify I tell it to use the most recent version. This could get out of whack as Amplify changes its base image. For instance initially their default Ruby version was 2.3 which was not compatible with Jekyll 4 so it didn't work and I needed to have an rvm command to tell it to use 2.6.x. This has changed in the few months I have been doing this. Now 2.4 is the default Ruby version so I commented out the rvm line in the build file.

On the Amplify console you can edit the build file. I don't use that, the build file is here in the repo that way it is easier to know how I have it setup and I can use it in other projects easier. Same goes for the custom headers file. There is a way to edit that on the Amplify console but it is overwritten if the file exists in the root which is what I have.

Be careful with assets, I have all my css and js files ending in a url parameter with a date-time stamp. The customHttp file is setting a long cache on all assets except html. If you don't do something to bust the cache then existing users (and you doing testing) may not see the new changes. I have had a really hard time getting Chrome or Edge to redownload cached files. 

I add `?v={{site.time | date: '%s'}}` to the end of my css and js files, like:  
` <link rel="stylesheet" href="/assets/css/site.css?v={{site.time | date: '%s'}}">`

## Can I use it without Amplify?

Sure, there isn't really anything in here that is special as far as the hosting is concerned other than the amplify and customHttp files. So you can delete those and just use the NPM scripts and host how ever you want.

