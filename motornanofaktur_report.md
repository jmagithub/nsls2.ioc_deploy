# motorNanoFaktur Role — Scaffolding Report

## What was done

**8 files were created** to scaffold the `motornanofaktur` device role:

| # | File | Description |
|---|------|-------------|
| 1 | `roles/device_roles/motornanofaktur/README.md` | Role description |
| 2 | `roles/device_roles/motornanofaktur/schema.yml` | Yamale validation schema — defines `type: "motornanofaktur"`, required environment vars (`ENGINEER`, `PORT`, `ASYN_PORT`, `P`, `R`, `PREFIX`, `CONTROLLER_IP`, `CONTROLLER_PORT`), and per-axis fields (suffix, addr, desc, egu, dir, velo, vbas, accl, bdst, bvel, bacc, mres, prec, dhlm, dllm, init) |
| 3 | `roles/device_roles/motornanofaktur/tasks/main.yml` | Ansible tasks — deploys `base.cmd`, copies `.template` db files, generates substitution file |
| 4 | `roles/device_roles/motornanofaktur/templates/base.cmd.j2` | EPICS startup script — loads dbd, registers driver, configures asyn IP port, loads motor substitutions |
| 5 | `roles/device_roles/motornanofaktur/templates/motornanofaktur.substitutions.j2` | Jinja2 template that generates EPICS substitution file from axis config |
| 6 | `roles/device_roles/motornanofaktur/files/motornanofaktur.template` | EPICS motor record template with autosave fields |
| 7 | `roles/device_roles/motornanofaktur/examples/motornanofaktur-01/config.yml` | Example 3-axis IOC config with placeholder values |
| 8 | `roles/install_module/vars/motornanofaktur_c9b0557.yml` | Install module definition — points to `https://github.com/NSLS2/motorNanoFaktur` at commit `c9b0557` |

**1 file was modified:**

| File | Change |
|------|--------|
| `roles/deploy_ioc/vars/motornanofaktur.yml` | Updated `deploy_ioc_required_module` from `motornanofaktur_FIXME` to `motornanofaktur_c9b0557`; set `deploy_ioc_executable: nanofaktur` and added `MOTOR_NANOFAKTUR` env var |

## Test results

All **11 automated tests pass**, 1 skipped:

- 7/7 passed in `test_device_roles.py` + `test_deploy_ioc_vars.py` + `test_install_module_vars.py`
- 4/4 passed in `test_validate_ex_configs_against_schemas.py`
- 1 skipped: `test_verify_yml_validates_with_schema` (no `verify.yml` exists yet)

## What remains to be done

| Priority | Task | File(s) | Details |
|----------|------|---------|---------|
| **High** | Update `base.cmd.j2` with real driver init calls | `roles/device_roles/motornanofaktur/templates/base.cmd.j2` | Currently uses a generic `drvAsynIPPortConfigure`. Needs the actual NanoFaktur-specific driver calls (e.g. `nanoFakturCreateController`, axis config, polling rates). Check the driver source in `nanoFakturApp/` for the correct function names. |
| **High** | Verify `deploy_ioc_executable` name | `roles/deploy_ioc/vars/motornanofaktur.yml` | Currently `nanofaktur` — must match the binary name built in `iocs/nanoFakturIOC/`. Check the `Makefile` in that directory for `PROD_IOC = ???`. |
| **Medium** | Update example config with real values | `roles/device_roles/motornanofaktur/examples/motornanofaktur-01/config.yml` | Replace placeholder IP `10.66.xx.xx`, PV prefixes, and axis parameters with realistic beamline values |
| **Medium** | Review schema axis fields | `roles/device_roles/motornanofaktur/schema.yml` | Currently copied from `motorpigcs2`. Add/remove fields if NanoFaktur axes need different parameters |
| **Low** | Add `verify.yml` | `roles/device_roles/motornanofaktur/examples/motornanofaktur-01/verify.yml` | Optional but recommended — defines file existence and content checks for deployment testing |
| **Low** | Review `motornanofaktur.template` | `roles/device_roles/motornanofaktur/files/motornanofaktur.template` | Currently a generic motor record template. May need fields specific to the NanoFaktur driver |
