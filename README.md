# Zilsd Steps

## Clone and install the requirements 
```bash
git clone https://github.com/vyoma-systems/zilsd
git checkout zilsd
pip install reporg; reporg -d $PWD/wd --list $PWD/riscof_repo_list.yaml
pip install git+https://github.com/vyoma-systems/riscof.git@zilsd
```

- Export zilsd compilable `spike` and `clang` paths 


## To generate Zilsd test using riscv-ctg:
```bash
cd ./wd/riscv_ctg
riscv_ctg -v debug -d ./generated_tests/ -r -cf ./sample_cgfs/dataset.cgf -cf ./sample_cgfs/rv32zilsd.cgf -bi rv32i -p2
```

## TO run Tests

- Check `Zilsd_Zcmlsd` is available at `ISA` section  in `spike_simple_isa.yaml` 
```bash
riscof run --config=config.ini --suite=./wd/riscv-arch-test/riscv-test-suite/ --env=./wd/riscv-arch-test/riscv-test-suite/env
```

## To generate coverage 
```bash
export CGF_HOME=$PWD/wd/riscv-ctg/sample_cgfs

riscof --verbose debug  coverage --cgf-file $CGF_HOME/dataset.cgf --cgf-file $CGF_HOME/rv32i.cgf  --cgf-file $CGF_HOME/rv32zilsd.cgf --cgf-file $CGF_HOME/rv32ic.cgf --suite=./wd/riscv-arch-test/riscv-test-suite/ --env=./wd/riscv-arch-test/riscv-test-suite/env
```
