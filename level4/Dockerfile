# This Dockerfile is based on the dockerfile 'fmriprep' from the Poldrack
# Lab (https://github.com/poldracklab/fmriprep). The jupyter notebook foundation
# is based on jupyter/docker-stacks's base-notebook.
#
# This means that the same copyrights apply to this Dockerfile, as they do for
# the above mentioned dockerfiles. For more information see:
# https://github.com/miykael/nipype_env

FROM miykael/nipype_level2
MAINTAINER Michael Notter <michaelnotter@hotmail.com>

#-------------------
# Install MATLAB MCR
#-------------------
USER root
RUN mkdir -p /opt/mcr_install /opt/mcr
ENV MATLAB_VERSION R2016b
RUN wget -P /opt/mcr_install https://www.mathworks.com/supportfiles/downloads/R2016b/deployment_files/R2016b/installers/glnxa64/MCR_R2016b_glnxa64_installer.zip
RUN unzip -q /opt/mcr_install/MCR_${MATLAB_VERSION}_glnxa64_installer.zip -d /opt/mcr_install
RUN /opt/mcr_install/install -destinationFolder /opt/mcr -agreeToLicense yes -mode silent
RUN rm -rf /opt/mcr_install && \
    rm /opt/mcr/v91/sys/os/glnxa64/libstdc++.so.6 && \
    ln /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.20 /opt/mcr/v91/sys/os/glnxa64/libstdc++.so.6

# Configure environment
ENV MCR_VERSION v91
ENV LD_LIBRARY_PATH /opt/mcr/${MCR_VERSION}/runtime/glnxa64:/opt/mcr/${MCR_VERSION}/bin/glnxa64:/opt/mcr/${MCR_VERSION}/sys/os/glnxa64:/opt/mcr/${MCR_VERSION}/sys/opengl/lib/glnxa64:$LD_LIBRARY_PATH
ENV MCR_INHIBIT_CTF_LOCK 1
RUN export LD_LIBRARY_PATH
ENV PATH=/opt/mcr/v91/bin:$PATH \
    MATLABCMD=/opt/mcr/v91/bin/glnxa64/MATLABWindow

#--------------------------------------
# Install SPM Standalone
#--------------------------------------
USER root
ENV SPM_VERSION=12 \
    SPM_REVISION=r6906
ENV SPM_DIR=/opt/spm${SPM_VERSION} \
    SPM_EXEC=${SPM_DIR}/spm${SPM_VERSION}
RUN wget -P /opt http://www.fil.ion.ucl.ac.uk/spm/download/restricted/bids/spm${SPM_VERSION}_${SPM_REVISION}_Linux_${MATLAB_VERSION}.zip && \
    unzip -q /opt/spm${SPM_VERSION}_${SPM_REVISION}_Linux_${MATLAB_VERSION}.zip -d /opt && \
    rm -f /opt/spm${SPM_VERSION}_${SPM_REVISION}_Linux_${MATLAB_VERSION}.zip && \
    ${SPM_EXEC} function exit

ENV SPMMCRCMD='/opt/spm12/run_spm12.sh /opt/mcr/v91/ script' \
    FORCE_SPMMCR=1

#-----------------------------
# Final Preparation & clean up
#-----------------------------
USER root
RUN ln -snf /bin/bash /bin/sh
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /boot /media /mnt /srv
RUN ldconfig
