Bootstrap: docker
From: ubuntu:bionic

%environment
    PATH="/home/miniconda3/bin:${PATH}"

%files
    deepribo.yaml

%post
    ## install wget and unzip
    apt-get update && apt-get install -y wget && apt-get install -y unzip && rm -rf /var/lib/apt/lists/*

    ## install conda
    wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /home/miniconda.sh
    bash /home/miniconda.sh -b -p /home/miniconda3
    rm /home/miniconda.sh
    export PATH="/home/miniconda3/envs/deepribo/bin:$PATH"

    ## update conda
    conda --version
    conda update -n base -c defaults conda
    conda --version

    ## create conda env and ensure that it runs on startup
    conda env create --file deepribo.yaml
    echo "source activate deepribo" > ~/.bashrc

    ## install r
    conda install -c conda-forge r-base r-sizer r-optparse

    ## download DeepRibo
    wget https://github.com/Biobix/DeepRibo/archive/master.zip
    unzip master.zip -d /home && rm master.zip

    ## add shebang and fix minor issues with DeepRibo
    sed -i '1 i\#!/usr/bin/env python' /home/DeepRibo-master/src/*.py
    sed -i '1 i\#!/usr/bin/env Rscript' /home/DeepRibo-master/src/*.R
    sed -i 's/model.load_state_dict(torch.load(model_name, map_location=device))/model.load_state_dict(torch.load(model_name, map_location=device),strict=False)/g' /home/DeepRibo-master/src/DeepRibo.py
    sed -i 's/sys.exit(ParseArgs())/ParseArgs()/g' /home/DeepRibo-master/src/DeepRibo.py

    ## cleaning up
    RUN cp /home/DeepRibo-master/src/*.py /usr/local/bin && cp /home/DeepRibo-master/src/*.R /usr/local/bin && rm -rf /home/DeepRibo-master/

    ## make files executable
    RUN chmod +x /usr/local/bin/DataParser.py
    RUN chmod +x /usr/local/bin/DeepRibo.py
    RUN chmod +x /usr/local/bin/PredictToBedgraph.py
    RUN chmod +x /usr/local/bin/s_curve_cutoff_estimation.R

    ## test commands
    RUN DeepRibo.py -h
    RUN DataParser.py -h
    RUN PredictToBedgraph.py -h
    RUN s_curve_cutoff_estimation.R
