# LIFECYCLE CONTAINERLAB
## First use:
    netlab create ./topology.yml
    containerlab deploy -t clab.yml
    netlab initial
    ! work on the lab, saving all work with 'wr mem'
    containerlab destroy -t clab.yml

## Subsequent visits:
    containerlab deploy -t clab.yml
    ! work on the lab, saving all work with 'wr mem'
    containerlab destroy -t clab.yml
