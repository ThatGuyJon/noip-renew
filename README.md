# No-IP Renewal

[noip.com](https://www.noip.com/) free hosts expire every month (every 30 days to be exact). This script checks the website to renew the hosts, using Python/Selenium with Chrome headless mode.

Based originally on [noip-renew](https://github.com/loblab/noip-renew) and made some minor improvements (improved logging, adding type hints, improved documentation).

## Prerequisites

- A No-IP account with configured DNS records is required. The method of maintaining IP addresses pointing to these records is outside the scope of this script.
- Your No-IP account must be secured with 2FA/TOTP. This script does not handle one-time codes sent via email.
  - **Enable Two-Factor Authentication (2FA) on your No-IP account and note the TOTP secret key. When setting up 2FA, you'll be presented with a QR code; the TOTP secret key is displayed below the QR code.**
- Python >= 3.10

## GitHub Actions Workflow Prerequisites

- Repository Secrets:
  - Go to your GitHub repository.
  - Navigate to **Settings → Secrets and variables → Actions**.
  - Click on "New repository secret".
  - Add the following secrets:
    - `NOIP_USERNAME`: Your No-IP account username.
    - `NOIP_PASSWORD`: Your No-IP account password.
    - `NOIP_TOTP_SECRET`: Your TOTP secret key (obtained above).

## Usage

1. Clone this repository
2. Install the pip lib:

```shell
pip install -r requirements.txt
```

3. Run the command:

```shell
python3 noip-renew.py -h
usage: noip DDNS auto renewer [-h] -u USERNAME -p PASSWORD -s TOTP_SECRET [-t HTTPS_PROXY] [-d DEBUG]

Renews each of the no-ip DDNS hosts that are below 7 days to expire period

options:
  -h, --help            show this help message and exit
  -u USERNAME, --username USERNAME
  -p PASSWORD, --password PASSWORD
  -s TOTP_SECRET, --totp-secret TOTP_SECRET
  -t HTTPS_PROXY, --https-proxy HTTPS_PROXY
  -d DEBUG, --debug DEBUG
```

## Usage with Docker

1. First, build the container image:

```shell
docker build -t noip-renewer:latest .
```

2. Then run it:

- Use `-v` to mount the screenshots path into your current directory.

```shell
% docker run -ti --rm -v ${PWD}/screenshots:/app/screenshots noip-renewer:latest -u "<YOUR_EMAIL>" -p "<YOUR_PASSWORD>" -s "<YOUR_TOTP_SECRET_KEY>"
```

## As a cronjob

Add the following to your crontab, e.g. everyday 1AM:

```shell
crontab -l | { cat; echo "0 1 * * * docker run -ti --rm -v ${PWD}/screenshots:/app/screenshots noip-renewer:latest -u '<YOUR_EMAIL>' -p '<YOUR_PASSWORD>' -s '<YOUR_TOTP_SECRET_KEY>'"; } | crontab -
```

## GitHub Actions Workflow

This repository includes a GitHub Actions workflow (`.github/workflows/noip-renew.yml`) to automatically renew your No-IP hosts. The workflow is triggered on a schedule (defined in the workflow file) and uses the repository secrets you configured in the prerequisites.

To use this workflow, clone the repository and configure the necessary secrets as described in the prerequisites section. You may run the No-IP Auto Renew workflow from the Actions tab. Or it will be executed automatically according to the defined schedule.
