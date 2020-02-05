Bootstrap: docker
From: ubuntu:bionic

%environment
export PATH="/home/miniconda3/bin:${PATH}"

%post
    ## install wget and unzip
    apt-get update && apt-get install -y wget && apt-get install -y unzip && rm -rf /var/lib/apt/lists/*

    ## install conda
    wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /home/miniconda.sh
    bash /home/miniconda.sh -b -p /home/miniconda3
    rm /home/miniconda.sh

    ## update conda
    conda --version
    conda update -n base -c defaults conda
    conda --version

    touch deepribo.yaml
    echo -e "name: deepribo\nchannels:\n - conda-forge\n - bioconda\n - anaconda\n - pytorch\n - defaults\ndependencies:\n - pytorch-cpu=0.4.1\n - torchvision-cpu=0.2.1\n - biopython=1.72=py35h04863e7_0\n - gffutils=0.9=py35_0\n - ca-certificates=2017.08.26=h1d4fec5_0\n - certifi=2017.11.5=py35h9749603_0\n - dbus=1.10.22=h3b5a359_0\n - jsonschema=2.6.0=py35h4395190_0\n - libedit=3.1=heed3624_0\n - libffi=3.2.1=hd88cf55_4\n - libgcc=7.2.0=h69d50b8_2\n - libgcc-ng=7.2.0=h7cc24e2_2\n - libstdcxx-ng=7.2.0=h7a57d05_2\n - numpy=1.13.3=py35hd829ed6_0\n - pandas=0.22.0=py35hf484d3e_0\n - pandoc=1.19.2.1=hea2e7c5_1\n - pandocfilters=1.4.2=py35h1565a15_1\n - pip=9.0.1=py35h7e7da9d_4\n - python=3.5.4=h417fded_24\n - python-dateutil=2.6.1=py35h90d5b31_1\n - scikit-learn=0.19.1=py35hbf1f462_0\n - setuptools=36.5.0=py35ha8c1747_0\n - simplejson=3.12.0=py35h592e862_0\n - sip=4.18.1=py35h9eaea60_2\n - six=1.11.0=py35h423b573_1\n - sqlite=3.20.1=hb898158_2\n - statsmodels=0.8.0=py35haa9d50b_0\n - tk=8.6.7=hc745277_3\n - wheel=0.30.0=py35hd3883cf_1\n - xz=5.2.3=h55aa19d_2\n - zlib=1.2.11=ha838bed_2" > deepribo.yaml;

    ## create conda env and ensure that it runs on startup
    conda env create --file deepribo.yaml
    echo "source activate deepribo" > ~/.bashrc
    export PATH=/home/miniconda3/envs/deepribo/bin:$PATH

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
