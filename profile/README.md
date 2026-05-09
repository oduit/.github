# oduit

Odoo developer tooling for repeatable development, testing, CI, and coding-agent workflows.

`oduit` is an open-source toolset for working with Odoo projects more safely and more consistently. The ecosystem focuses on practical Odoo workflows: addon discovery, manifest and dependency inspection, module installation and updates, test execution, runtime introspection, and reproducible tool packaging.

## Projects

| Project                                                 | Purpose                                                      |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| [`oduit`](https://github.com/oduit/oduit)               | CLI and Python library for Odoo development, addon intelligence, environment diagnostics, structured automation, and coding-agent workflows. |
| [`pytest-oduit`](https://github.com/oduit/pytest-oduit) | pytest plugin for running Odoo tests with automatic Odoo setup and `.oduit.toml` integration. |
| [`oduit.nix`](https://github.com/oduit/oduit.nix)       | Nix flake packaging `oduit`, `pytest-oduit`, and related Odoo development tools for reproducible installations. |

## `oduit`

[`oduit`](https://github.com/oduit/oduit) is the main CLI and Python utility layer.

It helps with common Odoo operations such as:

```bash
oduit doctor
oduit version
oduit list-addons
oduit list-installed-addons
oduit print-manifest sale
oduit list-depends sale
oduit install-order sale,purchase
oduit impact-of-update sale
oduit install sale
oduit update sale
oduit test --test-tags /sale
```

It also provides structured JSON-oriented commands for coding agents:

```bash
oduit agent context
oduit agent inspect-addon sale
oduit agent inspect-model res.partner
oduit agent inspect-field res.partner email --with-db
oduit agent validate-addon-change my_module --allow-mutation --update --discover-tests
oduit agent test-summary --module my_module --test-tags /my_module
```

Install it with:

```bash
pip install oduit
```

Or run it without installing:

```bash
uvx oduit
```

## `pytest-oduit`

[`pytest-oduit`](https://github.com/oduit/pytest-oduit) connects pytest-based test execution with Odoo and `oduit`.

It can load `.oduit.toml`, resolve addon paths, detect the addon under test from the pytest path, initialize required modules, and work with `pytest-xdist` for distributed test execution.

Basic usage:

```bash
pip install pytest-oduit

pytest addons/sale
pytest addons/sale/tests/test_sale.py
pytest -n auto
```

Typical use cases:

- run Odoo addon tests through pytest
- avoid manually passing the module name for every test path
- keep pytest configuration aligned with local `oduit` project configuration
- run isolated test databases for parallel workers

## `oduit.nix`

[`oduit.nix`](https://github.com/oduit/oduit.nix) provides reproducible Nix packaging for Odoo development tools.

Try the interactive launcher:

```bash
nix run github:oduit/oduit.nix
```

Run a specific tool:

```bash
nix run github:oduit/oduit.nix#oduit -- --help
nix run github:oduit/oduit.nix#pytest-oduit -- --help
nix run github:oduit/oduit.nix#odoo-ls -- --help
```

The flake includes packages such as `oduit`, `pytest-oduit`, `pytest-odoo`, `pylint-odoo`, `odoo-ls`, `odoo-lsp`, `manifestoo`, `click-odoo`, `whool`, and other Odoo-related utilities.

## Typical workflow

1. Add a local `.oduit.toml` to your Odoo project.
2. Use `oduit doctor` to validate the environment.
3. Use `oduit list-addons`, `print-manifest`, `list-depends`, and `install-order` to inspect the addon graph.
4. Use `oduit install`, `update`, and `test` for day-to-day module work.
5. Use `pytest-oduit` when pytest is the preferred test runner.
6. Use `oduit.nix` when you want reproducible tool versions across machines or CI.

Example `.oduit.toml`:

```toml
[binaries]
python_bin = "./venv/bin/python"
odoo_bin = "./odoo/odoo-bin"

[odoo_params]
addons_path = "./addons,./custom_addons"
db_name = "project_dev"
```

## For coding agents

`oduit` is designed to expose Odoo context in a structured, low-noise way.

Prefer read-only inspection first:

```bash
oduit agent context
oduit agent resolve-config
oduit agent inspect-addon my_module
oduit agent get-model-fields res.partner --attributes string,type,required
oduit agent locate-model res.partner --module my_module
oduit agent list-addon-tests my_module
```

Then run controlled validation:

```bash
oduit agent validate-addon-change my_module --allow-mutation --update --discover-tests
oduit agent test-summary --module my_module --test-tags /my_module
```

This makes Odoo projects easier to understand for both humans and automation without relying on large, ad hoc shell output.

## Links

- [`oduit`](https://github.com/oduit/oduit)
- [`pytest-oduit`](https://github.com/oduit/pytest-oduit)
- [`oduit.nix`](https://github.com/oduit/oduit.nix)
- [`oduit` on PyPI](https://pypi.org/project/oduit/)
- [`pytest-oduit` on PyPI](https://pypi.org/project/pytest-oduit/)
