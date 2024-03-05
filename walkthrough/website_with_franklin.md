+++
title = "Creating a website with Franklin.jl"

hascode = true
hasmath = false
date = Date("05/03/2024", "dd/mm/yyyy")

tags = ["walkthrough", "code", "julia", "web"]
+++

# Creating a website with Franklin.jl

Franklin is a Julia package which helps with creating static websites in Julia. Let's see how to set a website up, and publish it with Github Pages!

\toc


## Creating the website
### Installing Franklin.jl

If not already done, install Julia ([see here](/walkthrough/first_steps_julia) to see how).

Enter the Julia REPL (typing `julia` in a terminal). Type `]` to enter Pkg mode ([the docs](https://docs.julialang.org/en/v1/stdlib/REPL/) explain quite well how to use the REPL). Then, writing
```
add  Franklin
```
will install `Franklin.jl` for you. Yey!

### First website!

You can create a website as easily as this:
```
julia> using Franklin
julia> newsite("MyFirstSite")
```

Then, writing
```
julia> serve()
```

will start a server at `http://localhost:8000/` (which can be stopped with `Ctrl+C` in the terminal). Open this in your browser and tadam, you have a website!

It won't be beautiful though. To fix this, choose a template [over there](https://tlienart.github.io/FranklinTemplates.jl/); I am using Minimal Mistakes. Now type this in the REPL (replacing `my_template` with your choice):

```
julia> newsite("MyFirstSite", template="my_template")
```

Now, you can start filling you website with anything you want! Follow the [Franklin docs](https://franklinjl.org/) (which actually explain what I'm writing here in greater detail) and you template's docs to create the website.

## Publishing the Github page

### Version control: git
Version control is incredibly useful, and easy to learn (should write an article on that soon). I will assume here that you have `git` installed, set up and linked with your Github account. Go to [github.com](https://www.github.com), and create an empty repository with the name `username.github.io`, replacing `username` with your actual username in lowercase.

Now, open a terminal in your local website's folder, and type:

```bash
$ git init
$ git add .
$ git commit -m "First commit"
$ git branch -M main
$ git remote add origin git@github.com:Username/username.github.io.git
$ git push -u origin main
```

again replacing `Username` and `username` with your username, real and lowercased.

Franklin defined for you (in the `.github/workflows` folder) a Github action triggering every time you push commits to the remote repository. This will deploy your website to the `gh-pages` branch; we only have have to tell Github to get the files there. On the main Github page of your repo, you must wait for the orange circle to become a green check, indicating that the Github Action finished.

Go to `https://github.com/Username/username.github.io/settings/pages` (again, replacing `username` etc.), scroll down to `Build and deployment`. `Source` should be set to `Deploy from a branch` and `Branch` to `gh-pages` (leaving the second selection to `/ (root)`). You website should be up very soon (in a few seconds for me) at `https://username.github.io`; congratulations, you now have a personal webpage!


## Maintaining and checking the webpage

- Create correct favicon with [Real Favicon checker](https://realfavicongenerator.net/), then check with [the checker](https://realfavicongenerator.net/favicon_checker)
- Check for broken links with [broken-link-checker](https://github.com/stevenvachon/broken-link-checker#readme)

## Troubleshooting

Again, the [Franklin docs](https://franklinjl.org/) are great, so give them a go if you lack some information ([here](https://franklinjl.org/workflow/deploy/#deploying_on_github) for the deployment to Github pages)