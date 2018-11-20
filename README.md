# Suggested configuration for an accessibility audit
The OpenAssesIt Toolkit is a suite of projects that are meant to be run independently or as a suite. Below is a suggested configuration. 

This project will take a Lighthouse JSON report, convert it to MarkDown, then take screenshots of all the failing elements.

This project can work on all audit categories, but current efforts are focused on the accessibility audits.

## Two options to get started:

__1. Run in local Docker container__

OR

__2. Manually install prerequisites and run natively__

---

### 1. Run in Docker container

This is the easiest way to get set up. 

#### Prerequisites 

Download and Install Docker for your OS:

[Docker for Mac](https://docs.docker.com/docker-for-mac/install/)

[Docker for Windows](https://docs.docker.com/docker-for-windows/install/)

#### Setup

Download OpenAssessIt:

```
git clone https://github.com/OpenAssessItToolkit/openassessit.git -b develop/add_docker
```

Change directory:

```
cd openassessit
```

#### Run it

Run OpenAssessIt in the container:

```
docker-compose -f docker-compose.local.yml build
docker-compose -f docker-compose.local.yml run app https://cats.com catshomepage firefox
docker-compose -f docker-compose.local.yml down
```

The audit will be copied into your `openassessit/tmp/` directory

---

# OR

### 2. Manually install prerequisites and run locally

#### Prerequisites

1. Don't be intimidated, you can do this.

2. Verify that [Chrome Lighthouse](https://github.com/GoogleChrome/lighthouse/) is [installed](https://github.com/GoogleChrome/lighthouse#using-the-node-cli) `lighthouse --version`
3. Verify that [Python](https://www.python.org/) is [installed](https://realpython.com/installing-python/), preferaby Python 3.6+, `python --version` or `python3 --version`
4. Verify that [PIP](https://pypi.org/project/pip/) is [installed](https://www.makeuseof.com/tag/install-pip-for-python/) `pip --version` (Note Pip already comes with Python 2.7.9+ and 3.4+)
5. Verify that a webdriver is [installed](https://pypi.org/project/selenium/#drivers) (Firefox gecko or Chrome)
6. Run `pip install -r requirements.txt` from the root of this repo to install Selenium, Jinja2, and Pillow.
7. Read the README.md files.

#### Setup

From your projects directory:

__1) Install requirements:__

```
pip install -r requirements.txt
```

__2) Clone OpenAssessIt and OpenAssessIt Templates:__

```
git clone https://github.com/OpenAssessItToolkit/openassessit.git
```
```
git clone https://github.com/OpenAssessItToolkit/openassessit_templates.git
```

#### Run

__1) Create a Lighthouse json accessibility audit file to import__


```
lighthouse https://cats.com \
--only-categories=accessibility \
--disable-device-emulation \
--output=json \
--output-path=catsaudit.json \
--chrome-flags="--headless --window-size=1300,600"
```
Or use our [custom Lighthouse accessibility audit recipe](https://gist.github.com/joelhsmith/21bb103e987da65c67f6420488643380)

__2) Run Lighthouse to Markdown__

Converts the json file to markdown.

```
python openassessit/markdown.py \
--input-file="catsaudit.json" \
--output-file="catsaudit.md" \
--user-template-path="/abs/path/to/openassessit_templates/templates/"
```

__3) Run Capture Assets__

Looks for failed audit items in the json file and create a screenshots of each offending element and saves them in an 'assets' folder.

```
mkdir assets
```

```
python openassessit/capture.py \
--input-file="/abs/path/to/catsaudit.json" \
--assets-dir="/abs/path/to/assets/" \
--sleep=1 \
--driver=firefox
```


---

## Make friends and influence people


```
open catsaudit.md
```

Your markdown file is complete. You can use it as-is, or augment the content with additional custom help text.  I usually use MacDown and save them out as html files and host them on GitHub pages.

## Notes

1. If you want to change the order of the audits in the Markdown file, [custom Lighthouse config](https://gist.github.com/joelhsmith/21bb103e987da65c67f6420488643380) and change the weight `[categories][accessibility][auditRefs][id][weight]`

2. The report creates images from elements listed in 'color-contrast', 'link-name', 'button-name', 'image-alt', 'input-image-alt', 'label', 'accesskeys', 'frame-title', 'duplicate-id', 'list', 'listitem', 'definition-list', 'dlitem'.

This project is only possible because of [ihadgraft](https://github.com/ihadgraft)'s generous donation of his expertise, time, and patience with [joelhsmith](https://github.com/joelhsmith).  Thank you Iain!
