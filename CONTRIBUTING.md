# Contributing

Contributions are encouraged. Please fork this project, make changes and submit
a PR. All changes are expected to pass yamllint, ansible-lint and molecule tests prior
to merge.

## Development requirements

* Ansible
* Docker
* Molecule

## Setting up the development environment

1) Clone the repository

        $ git clone git@github.com:jvoss/ansible-role-netbox.git

2) Create and activate a virtual environment

        $ python -m venv venv
        $ source venv/bin/activate

3) Install the requirements

        $ pip install -r requirements.txt

## Testing Changes

Yamllint:

      $ yamllint .

Ansible Lint:

      $ ansible-lint .

Molecule testing (will also run ymllint and ansible-lint):

      $ molecule test

**WSL2 Note**: If developing on Windows, WSL2 containers lack the ability to run
systemd and therefore will not be able to be tested with molecule.
