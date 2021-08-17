# SEAL website
This is the repo that hosts the information used in the [SEAL website](https://ncsu-seal.netlify.app/), which is from the Academic Template for [Hugo](https://github.com/gohugoio/hugo) (see the documentation [here](https://wowchemy.com/docs/) for specific info). The goal for this website is that it's essentially a bunch of markdown files that anyone in the lab can update when needed, so this is meant to be a collaborative effort.

The URL for the website is currently the basic, free one (ncsu-seal.netlify.app). If we want to have a custom domain, that is easy to do but there is a small cost per year).

## Updating Content
Content can either be edited directly in GitHub or by cloning the repository to your local machine, editing files, and pushing back to GitHub. Website deployments (rendering the website) are done automatically with each commit via Netlify, so it's advised that you clone the repo when possible (e.g. push several commits at once, instead of one for each small change). 
- *NOTE:* currently the Netlify account is linked to Ian's GitHub, but anyone can have access if they want. Please send an email to him asking to be added.

### Lab member info
Each lab member has their own "page" on the website, under the [Team section](https://ncsu-seal.netlify.app/people/). To update your page, navigate to your [folder](https://github.com/ncsuSEAL/sealWebsite/tree/master/content/authors) and update the information in `_index.md` as appropriate. To add a profile picture, upload an image labeled `avatar.jpg` (can be png) that preferably is <= 1 MB.
- Each lab member belongs to a different category. These can be seen (and/or edited) in `content/people/people.md`. Note that the attribution must be spelled and capitalized exactly the same.
- If you do not have one of the social options (e.g. you don't have a Twitter), then remember to comment out those lines with `#`.

### Add a lab update
To add lab update (i.e. short post like a blog or news announcement), navigate to `content/post` and create a folder starting with the date and a short qualifying of the name, for example "2021-08-15_testPost". Inside the folder create a file named `index.md` that files the standard toml format; see the [sample post](https://github.com/ncsuSEAL/sealWebsite/tree/master/content/post/2021-08-15) for an example. You can also add an associated image (`image.png`) if you want.

### Add a publication
To add a publication, navigate to `content/publications` and create a folder labeled as Date-journalAbbr-descriptor, so for example "2021-01-04_AWM-Perin-Tulbure" or "2020-12-07_AGU-southeastern-Gaines" (these examples come from Mirela's site). 

Inside each folder you can have the following 4 items
- citation file (`cite.bib`): this is a BibTex file with the citation for the paper. If this is added, then it allows people to download the citation from the lab website.
- pdf of the paper (`<someName>.pdf`): self-explanatory, just note you will need to have this file name referenced in the `index.md` file.
- an associated image (`featured.jpg`): some image if you want, otherwise the publication will show up only with text on the website.
- `index.md`: this is the primary metadata for hoow the website renders the publication info. Similar to other index files, this has a specific toml format, so make sure everything is filled out as needed. Remember, if other lab members are on the publication, refer to them using their username on the website, which is all lowercase (e.g. joshgray). This way their user page is linked to the publication.

### Default info for the website
To change around how the website looks, including color, background pictures, etc, see the following, plus the Wowchemy documentation (linked at top) for more details.
- `content/home` controls the home page
- `config/_default/` controls the underlying metadata and parameters

## Troubleshooting
What happens if the website hasn't updated despite you making changes?
- Most likely there the rendering process didn't find something where it thought it should. If you don't have access to the Netlify account, either ask Ian to check or ask for access. Usually this is because everything must be in its specific place with a specific name (and references to different files also need to be matched). This can take some trial and error, but one thing that can help is comparing with a fully-done website, like Mirela's [site](https://practical-pike-e67a8a.netlify.app/) and the associated [GitHub repo](https://github.com/MirelaGTulbure/gaec-lab).
