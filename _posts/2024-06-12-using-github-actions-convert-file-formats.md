---
layout: category-post
title:  "Using Github Actions to Convert File Formats"
categories: website
---
This post is all about learning and coding in public. We learn from each others mistakes and we get better by building together.

* * *

One of my favorite longterm projects has been [RELCFP](https://relcfp.com). It is a simple site that aggregate [religious studies call for papers](https://relcfp.com). The site works by pulling in a [custom RSS Feed](https://input.relcfp.com) and CFPs that have been submitted via the site. I like to blend the data together so that people can quickly and easily get to the main location of these call for papers. Recently the script I have been using  broke. ```XMLHttpREquest``` is no longer recommended as a way to display and style XML. Here is the broken script 🪦.

```
    <script type="text/javascript">
        function loadXMLDoc(dname) {
          xhttp = new XMLHttpRequest();
          xhttp.open("GET",dname,false);
          xhttp.send("");
         return xhttp.responseXML;
        }
   
        var processor = new XSLTProcessor();
        var theXML = loadXMLDoc('feed.xml');
        var theXSL = loadXMLDoc('feed.xsl');
   
        // prepare the processor
        processor.importStylesheet(theXSL);
   
        var theResult = processor.transformToDocument(theXML);
        // now you have a DomDocument with the result
        // if you want to serialize (transform to a string) it you van use
   
        document.write(new XMLSerializer().serializeToString(theResult));  
     </script>

```

I am working on several ways to replace this script. First I replaced my old FreshRSS server with a wonderful [Eleventy](https://11ty.dev) starter theme called [Multiplicity-M10ty](https://github.com/lwojcik/eleventy-template-multiplicity) by [@lwojcik](https://github.com/lwojcik/). Now instead of a bland RSS feed and yet another server bill I have a nicely [stylized feed](https://input.relcfp.com/) with clear attributions. Plus this new site is much easier to upate.

Now for part 2 fixing the homepage of [relcfp.com](https://relcfp.com). I want and need an automated and easy way to style the data. I am able to easily import the RSS. Now I just need to style it. Styling the XML is proving rather difficult. One of the solutions I am exploring is convering the XML into JSON and styling JSON something which is a much clearer and more well documented process. Using CI/CD I want to create a Github Action that will automatically convert XML to JSON and then commit the new JSON file to the repository.

I found a really cool github action ["Data Format Converter Action"](https://github.com/fabasoad/data-format-converter-action) by [@fabasoad](https://github.com/fabasoad). Through lots of trial and error and kind assistance by fabasoad. I wwas able to get the Github Action **almost** working on [my repository](https://github.com/adamdjbrett/data-format-converter-action-demo/). Presently my issue is that [the script](https://github.com/adamdjbrett/data-format-converter-action-demo/blob/main/.github/workflows/xml2yml.yml) runs but it does not output and add a new results.json to the repository. 

## Setting up ["Data Format Converter Github Action" by @fabasoad](https://github.com/fabasoad/data-format-converter-action)
Here is what I have learned for how to setup this Github Action.

### Current Instructions
The [current instructions](https://github.com/fabasoad/data-format-converter-action/blob/main/docs/Examples.md) are rather threadbare but thanks to the assistance of @fabasoad I was able to get them working. 

>
>
>### Option 1: Personal access token (PAT)
>
>1. [Create PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic)
>   with `repo` permission.
>2. [Create repository secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)
>   (e.g. `MY_GITHUB_TOKEN`) with the PAT value.
>3. Use the following configuration:
>
>```
>yaml
>- uses: fabasoad/data-format-converter-action@v0
  >with:
    >input: "person.xml"
    >from: "xml"
    >to: "yaml"
    >token: ${{ secrets.MY_GITHUB_TOKEN }}
>```
>
>

### My comments
In this section please make sure that you create your ``MY_GITHUB_TOKEN``` via the ```Personal access tokens (classic)``` method. I tried the newer approach but I could not get it to work. if you can please let me know.

>
>
>
>### Option 2: GitHub App token
>
>1. [Register a GitHub App](https://docs.github.com/en/apps/creating-github-apps/registering-a-github-app/registering-a-github-app)
>   with `contents: read` permission.
>2. Create private key and save it somewhere on your local disk.
>3. [Install GitHub App](https://docs.github.com/en/apps/using-github-apps/installing-your-own-github-app)
   >to your repository.
>4. [Create repository secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)
   >(e.g. `APP_PRIVATE_KEY`) with the private key created on step 2.
>5. Create repository variable (e.g. `APP_ID`) with the GitHub App ID.
>6. Use the following configuration:
>
>```yaml
>- uses: actions/create-github-app-token@v1
  >id: generate-app-token
  >with:
    >app-id: ${{ vars.APP_ID }}
    >private-key: ${{ secrets.APP_PRIVATE_KEY }}
>- uses: fabasoad/data-format-converter-action@v0
  >with:
    >input: "person.xml"
    >from: "xml"
    >to: "yaml"
    >token: ${{ steps.generate-app-token.outputs.token }}
>```
  
  
### My Comments
This section really tripped me up. Creating the app and installing the app take time but the setup eventually was fairly direct. Here is a screenshot of the settings I used.
[![Screenshot of the App Settings click to enlarge](/assets/img/screencapture-github-settings-apps-adjb-dfca-2024-06-11-20_03_59_tn.jpg)](screencapture-github-settings-apps-adjb-dfca-2024-06-11-20_03_59_tn.jpg)

For permissions they need to be for content and metadata. 
When installing the APP I chose to limit to just the repositories I am using it on for this project for added security.

When Creating your APP you will be asked to create a ``private key``` and this key will download to your computer. You are going to copy and paste that whole key into the ```APP_PRIVATE_KEY``` that you create on your repository. Likewise you will want to use the APP_ID and not the Client_ID as your vairable. 

## Setting up the App
I had a lot of issues trying to figure out the keys. If you are having trouble, I do recomend walks to give your brain a break and to reset. I did not take enough breaks and that actually, ironically slowed me down and made this whole process take way longer.

### Current Settings

```yml
# This is a basic workflow to help you get started with Actions

name: XML2JSON #Change the Name

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the "main" branch
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:

  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      - uses: actions/checkout@v4

      # Runs a set of commands using the runners shell
      # name: Use Github App
      - uses: actions/create-github-app-token@v1
        id: generate-app-token
        with:
          app-id: ${{ vars.APP_ID }}
          private-key: ${{ secrets.APP_PRIVATE_KEY }}
      # name: Data Format Converter Action
      - uses: fabasoad/data-format-converter-action@main
        id: xml2json
        with:
          input: "person.xml"
          from: "xml"
          to: "json"
          token: ${{ steps.generate-app-token.outputs.token }}
      - name: Print result
        run: echo  '${{ steps.xml2json.outputs.output }}' > result.json
      - name: Print result
        run: echo  '${{ steps.xml2json.outputs.output }}' > result.json
      - run: cat result.json
      - name: Configure Git
        run: |
          git config --global user.name 'adamdjbrett'
          git config --global user.email '22662978+adamdjbrett@users.noreply.github.com'
      - name: Commit and push changes
        run: |
          git add result.json
          git commit -m "Save output to file"
          git push
        env:
          GITHUB_TOKEN: ${{ secrets.MY_GITHUB_TOKEN }}

```

Presently I am stuck on what do I do next, how do I output the demo data to a json file and commit that file to my repository? After I commit the file to my repository, then I can move over to trying to do this with a full RSS XML feed and see how well that works.

In order to get the "Configure Git" step to work I followed this article: ["How to solve "Permission to x denied to github-actions[bot]""](https://www.raulmelo.me/en/til/how-to-solve-permission-to-x-denied-to-github-actions-bot).

Following that article I was able to get my file to save and commit to the repository.

What wound up working was two things first sanitzing the json thanks to [@spenserblack](https://github.com/spenserblack)

```yml
name: Print result
run: echo "$RESULT" > feed.json
env:
  RESULT: ${{ steps.xml2json.outputs.output }}
```

Second switching to python thanks to [@imajeetyadav](https://github.com/imajeetyadav)
```yml
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install xmltodict
      - name: Data Format Converter Action
        shell: python
        id: xml2json
        run: |
          import xmltodict
          import json
          def validate_json(json_string):
            try:
                json.loads(json_string)
                return True
            except ValueError as e:
                print(f"Invalid JSON: {e}")
                return False
          with open('feed.xml', 'r') as xml_file:
              xml_data = xml_file.read()
              xml_dict = xmltodict.parse(xml_data)
              json_data = json.dumps(xml_dict, indent=4)
              with open('feed.json', 'w') as json_file:
                  json_file.write(json_data) 
```

[The current full file may be viewed in the repo](https://github.com/adamdjbrett/relcfp/blob/main/feed.json)


## Thank you to:
- [@fabasoad](https://github.com/fabasoad) on github
- Mahboob Ahmed on Stack Overflow
- [Raule Melo](https://www.raulmelo.me)
- [@imajeetyadav](https://github.com/imajeetyadav)
- [@spenserblack](https://github.com/spenserblack)

Thank you to all of them for being willing to learn, create, and help in public.

Now to apply this to my main repo. [relcfp.com](https://relcfp.com)

## Update
Everything is working great now. Please go check out the site [relcfp.com](https://relcfp.com)