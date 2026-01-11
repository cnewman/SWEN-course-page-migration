---
title: 'React Setup'

weight: 100
bookToC: true
bookSearchExclude: false

draft: true
---

## React.js Front End Setup using Vite

In these instructions, we’ll guide you through creating a basic React.js project.

{{% steps %}}

1. You’ll be using the same repository (`client-abc123`) as you did in Client0 and Client1.
    * For the remaining parts of the client project (client-2, client-3, client-4), we will create invidiual React apps. (See instructions below)

2. Install [Node.js](https://nodejs.org/en/). We recommend the latest long term support version (**v22.14.0 at the time of this writing**). However, if another project on your system requires the development version of Node.js, that is fine. Managing multiple versions of Node.js on a single system is often painful.

3. The installer will ask if you want to install **C/C++ compiler tools**. Do this. Many Javascript modules have native code that should be compiled locally. This will also install Python 3.13. TIf you run into problems, you may need to uninstall the new python version and revert back to python 3.12.

4. To test that Node.js is installed, go to the command line. Run `node --version`. You should see the version that you expect to be installed. You should also check your python version.

5. To create our app with useful development tooling, we’re using Vite. The [Vite introduction instructions are here](https://vite.dev/guide/) if you need them. Use this command (replacing `abc123` with your username):

    ```
    npx create-vite abc123-react-client2 -- --template react
    ```

    **NOTE:**

    * If you are prompted for a framework after this (e.g. Vanilla, React, Vue …), select `React`.
    * If you are prompted for a ‘variant’ (e.g. Javascript, Typescript, Tanscript, React Router …), select `Javascript`

6. The instructions have you cd into the directory, then install, then run. Like this:

    ```
    cd abc123-react-client2
    npm install
    npm run dev
    ```

    Later, you will have `-client3, -client4`. This creates a folder and a ton of files. Ordinarily, you’d only be doing this once at the beginning of your project. But in our case, we want you to re-create the base code to make grading a little easier.

    At this point, your directory structure should look something like this:

    ```
    ├── abc123-react-client2
    │   ├── node_modules
    │   ├── public
    │   │   └── vite.svg
    │   ├── src
    │   │   ├── App.css
    │   │   ├── App.jsx
    │   │   ├── assets
    │   │   │   └── react.svg
    │   │   ├── index.css
    │   │   └── main.jsx
    │   ├── eslint.config.js
    │   ├── index.html
    │   ├── package-lock.json
    │   ├── package.json
    │   ├── README.md
    │   └── vite.config.js
    ├── public/
    │   ├── index.html
    │   └── index_1.html
    ```

7. We need to make a few more changes to get the dev environment to autostart the web browser
Edit the file package.json, and change

    ```json
        "dev": "vite",
    to 
        "dev": "vite --open"
    ```

    Also, to make sure the production build sets the correct home/ basepath location, change

    ```json
        "build": "vite build",
    to
        "build": "vite build --base=./ "
    ```

8. We also like to make sure we have a visible build version on our page, and to make that display correctly (it is controlled by a script in `.gitlab-ci.yml`), we need to fix an annoying layout problem in the Vite stylesheet. Locate the file `src/index.css`. You should find the section

    ```css
    body {
    margin: 0;
    display: flex;
    place-items: center;
    min-width: 320px;
    min-height: 100vh;
    }
    ```

    We want to delete the `display` tag, so it looks like this:

    ```css
    body {
    margin: 0;
    place-items: center;
    min-width: 320px;
    min-height: 100vh;
    }
    ```

    Make that change, save, and then proceed to the next step.

9. If all goes well, if you are in `abc123-react-client2` folder on the command line, you should be able to run `npm run dev`. This will start a development server locally, and it fires up a browser automatically at `http://localhost:5173`. Note that the browser auto-starts courtesy of the change we made to the package.json file!

10. Now is a good time to commit to Git. Commit this starter code and push to your repository on RIT’s GitLab. Make sure that works before moving forward.

11. Let’s test out modifying code locally and then showing up in the browser. Open up src/App.jsx and change the text `Edit src/App.jsx and save to reload` to something else. Switching back to your browser, you’ll see the change is updated (it *should* auto-reload, but this can be a little flakey, so you may need to refresh the browser manually).

12. Take a tour of the starter code for a bit. Make a guess in your mind as to what each file does, then check your guess against our explanation down below in [our walkthrough](#reactjs-vite-starter-code-walkthrough). *Don’t rush this step. Knowing all of these pieces is important*.

13. Let’s make sure our changes will get reflected in the app. Go to `src/App.jsx` and change the `<h1>` tag to `<h1>Vite + React + SWEN 610</h1>`. It should change in the browser window immediately after saving the file.

14. Commit in Git and push to your GitLab repository.

15. Next, let’s get this up and running on our CI. For this project, we’re going to be emphasizing **continuous delivery**, which means that every build that succeeds on the repository will be posted to a website. Fortunately, the [GitLab pages](https://docs.gitlab.com/user/project/pages/) feature allows us to post code from our repository into a static site associated with the project. So let’s make use of that. Use this setup for your `.gitlab-ci.yml` Since you will have separate React apps for client-2 onward, **you will need edit the .yml file for each week**. For example `PROJECT_NAME2` (for client2) would be `abc123-react-client2`. When you do Client3, you would set `PROJECT_NAME3` would be `abc123-react-client3` and so on. Note that your client-2 webpage will be in a subdirectory of the main URL. Once again, you can see the root of the website in the gitlab deploy/pages. We have also added the full URL in the output of the CI. Make sure you check that!

    ```yaml
    image:
    name: node:22

    variables:
    CURR_APP: "" #Will be changed to your React project name.  Do not modify
    PROJECT_NAME2: none #e.g. abc123-react-client2.  Change to YOUR project
    PROJECT_NAME3: none  #e.g. abc123-react-client3.  Change to YOUR project
    PROJECT_NAME4: none  #e.g. abc123-react-client4.  Change to YOUR project
    OUTPUT_DIR2: public/$PROJECT_NAME2
    OUTPUT_DIR3: public/$PROJECT_NAME3
    OUTPUT_DIR4: public/$PROJECT_NAME4
    URL_APP2: $CI_PAGES_URL/$PROJECT_NAME2
    URL_APP3: $CI_PAGES_URL/$PROJECT_NAME3
    URL_APP4: $CI_PAGES_URL/$PROJECT_NAME4

    .script-run-build: &script-run-build |
    echo "CURR APP:"$CURR_APP
    #Make sure directory for the app exists
    if [  ! -d "$CURR_APP" ]; then
        echo "CURRENT APP not set, or directory does not exist - nothing to build!"
    else
        cd $CURR_APP
        echo "Create subfolder in gitlab public folder"
        if [ ! -d ../public ]; then
        echo "Creating ../public"
        mkdir ../public
        else
        echo "../public exists"
        fi
        mkdir ../public/$CURR_APP
        npm install

        # Add build info.
        sed -i "s|</body>|<footer style=\"position:absolute;left:0;bottom:0;font-size:9pt\">Built on GitLab branch <code>$CI_COMMIT_REF_NAME</code> commit <code>$CI_COMMIT_SHORT_SHA</code> $(date)</footer></body>|g" index.html

        npm run build
        echo "Copy build folder to gitlab public folder"
        cp -p -r dist/* ../public/$CURR_APP  
        echo "Return to the root directory"
        cd ..
    fi  

    cache:
    paths:
    - node_modules/

    before_script:
    - pwd
    - python3 --version
    - node --version
    pages:
    stage: deploy
    script:

        - CI="" #Ignore warnings.  e.g. unused classes are warnings but will stop build w/o this
        - echo "Check current directory"
        - pwd
        - CURR_APP=$PROJECT_NAME2
        - *script-run-build
        - CURR_APP=$PROJECT_NAME3
        - *script-run-build
        - CURR_APP=$PROJECT_NAME4
        - *script-run-build
        - echo "URL for CLIENT-2:"$URL_APP2
        - echo "URL for CLIENT-3:"$URL_APP3
        - echo "URL for CLIENT-4:"$URL_APP4
    artifacts:
        paths:
        - public
    ```

16. Let’s break down the above config.
    * For `image:` we’re just using a standard nodejs image from Dockerhub - nothing special like we had on our previous projects.
    * The `cache:` key is for speeding up our builds - we don’t want `npm` to have to download ~150mb of stuff every time we build, so we save the node_modules directory.
    * For `stage:` we’re using `deploy` instead of `test` - GitLab has various stages and so far we have only used `test`. There’s all kinds of logic you can build in there, e.g. “don’t `deploy` unless `test` passes”.
    * The `script:` line starting with `sed` has a magical command using [Bash](https://www.gnu.org/software/bash/) that needs explanation. When we build the site and put it on GitLab pages, we want to know what was actually posted. For example, a build might fail so then you’re looking at that *previous* commit’s build. Feel free to use, not use, or customize this as you see fix. This line isn’t strictly necessary, but trust me, you’ll be glad you have it when you realize you’re looking at an old build.
    * The `npm run build` makes a [production build](https://legacy.reactjs.org/docs/optimizing-performance.html), which bundles and minify the project into as few and smallest files as possible with some backwards compatibility.
    * The `rm` command removes our React `public` folder since it would conflict with GitLab’s `public` folder.
    * The `mv` command moves our build to where GitLab expects it to be.
    * The `artifacts:` part tells GitLab pages to save the public folder after our build finishes - so it can be used for GitLab pages.
    * You should also know that, in order to post to a GitLab pages site, the job *must* be called `pages` - that’s how it knows to put everything on a static site.

17. After reviewing the CI build settings, **commit and push to your repo**. Check that the build passes.

18. Once the build has passed, go to your project on GitLab, and go to **Settings > Pages** to see the URL that your code is posted at. Go there and keep note of that URL for the future so your teammates can see your latest build. Note that there’s our build information at the bottom of the page, whereas we don’t have that in our development build. Also note that it’s UTC time, which is four hours ahead of New York’s time zone.

{{% /steps %}}

## React.js Vite Starter Code Walkthrough.

* `node_modules` - this directory is infamous. Yes, after creating a basic React.js app that directory now has over 33 thousand files! This is all of the code that the framework depends on, which is the culimination of a massive community of open source developers. No need to ever touch this directory yourself - using `yarn` or `npm` commands essentially manages this directory. In a pinch, deleting this directory and reinstalling is the node.js equivalent of “have your tried turning it off and on again?”. Also: no need to put this in your Git repo - hence our .gitignore file ignoring it.
* `.gitignore` - you’ve seen this before. This one in particular is anticipating that we’re using a lot of things we haven’t turned on yet.
* `eslint.config.js` - this is a config file for a *linter*. A linter is a tool that checks for style “smells” in your code. Your code may not be “wrong” but it is not conforming to what React/JS/etc developers expect. This config uses React-specific linting - defaults are fine for us.
* `public/` is the directory where we keep our static content. Anything that just needs to be copied over to the final site should go here: images, metadata, etc. Anything that needs preprocessing, compiling, bundling, minifying, or any kind of munging does NOT go here.
* `public/vite.svg` - this is that [little icon you see in your browser tab](https://en.wikipedia.org/wiki/Favicon). Sometimes this is actually called `favicon.ico` - perhaps the only time in your life where people still use the `.ico` image format (other formats are allowed by the way, and [ICOs are just bundles of PNGs with zooming metadata](https://en.wikipedia.org/wiki/ICO_(file_format))).
* `public/index.html` - the root of our website. Most web servers are configured that when a url goes to a directory, then it opens up index.html. You will be porting your old code over to here, but then primarily working in the src/ folder as we start using React.js components for most things.
* `src/` is the root of our React app. When we get to using React.js, we’ll be living most of our development lives here. Notice how we have various file types in here.
* `src/App.css` and `src/index.css` are our main [Cascading Stylesheets](https://developer.mozilla.org/en-US/docs/Web/CSS). Why isn’t this in `public/`? Because React will actually check your CSS for validity, and you can also do some preprocessing with packages like [SASS](https://sass-lang.com/) to make your CSS cleaner to read and easier to maintain.
* `src/App.jsx` is the top-level React component. For now it does nothing.
* `src/main.jsx` is the entrypoint to our React project. Note that this is what instantiates `App`.
* `package.json` - this is used by `yarn` and `npm` to manage our packages, as well as some configuration options for React builds. This file and `yarn.lock` are consulted when managing `node_modules`. We made a small edit earlier for `homepage` - this setting tells React that we want our links to all be relative to the root directory, not absolute to the URL. This means that our site can be deployed at any URL path (e.g. both `localhost:3000/` and `example.com/foo/bar/baz/`). This is especially helpful since GitLab pages deploys the site at a deeper path than our development server does. [Learn more](https://create-react-app.dev/docs/deployment/#building-for-relative-paths) if you like.
* `package-lock.json` is a file that “freezes” what version of the packages you are using. For example, your `package.json` might say *use package foo 1.0*, but then package foo comes out with `1.0.1` and then `1.0.2`. So which one are you *actually* using? The `package-lock.json` file is the definitive authority on that. `npm` consults this file when figuring out what to put in `node_modules`.
* `README.md` has lots of React-related stuff to help you on your way. You may find the browser extension helpful, but it is not required.
* `vite.config.js` has the configuration details for how we’ll build all this together. Not much here, we could have set the base path here, [the docs explain how](https://vite.dev/config/shared-options.html#base), but we did this in package.json so we can have a flexible build process.

Here are some other files you might see in your startup, or other startup libraries:

* `public/manifest.json` - this is a configuration file that gives various metadata required for React to use some extra browser features, [learn more](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json) if you like.
* `public/robots.txt` - this is a file that is a [convention](https://en.wikipedia.org/wiki/Robots.txt) for how a (friendly) search engine should treat your website. It is just advice for doing search engine optimization for Google, Bing, etc. This one happens to default to “index everything on our site”.
* `src/setupTests.js` is the entrypoint for the testing framework, in this case this is what invokes a testing framework [Jest](https://jestjs.io/)
* `src/serviceWorker.js` has some boilerplate for improving performance of the React runtime. It also does some extra work to make development easier locally, i.e. when deployed on `localhost`. In a real system you might tweak this to your needs, but we won’t be doing any work here.
* `src/logo.svg` is in the `src` folder, but it could also probably be in the `public/` folder. SVG files are XML, so React might do some validation against it too as part of the build process, so that might be why it’s here. Or, perhaps the code will want to modify the SVG on the fly, in which case the SVG internal code itself is accessible to React.js. Typically, we put SVG files into `src/` and not `public/` for this reason.