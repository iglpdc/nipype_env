# This Dockerfile is based on the dockerfiles from 'crn_base' and 'crn_nipype'
# from the Poldrack Lab (https://github.com/poldracklab/crn_base), the
# dockerfiles from neurovault (https://github.com/NeuroVault/NeuroVault) and
# the dockerfile biss2016-notebook from Oscar Esteban under
# https://hub.docker.com/u/oesteban.
# The jupyter notebook foundation is based on jupyter/docker-stacks's
# base-, minimal- and scipy-notebook.
#
# This means that the same copyrights apply to this Dockerfile, as they do for
# the above mentioned dockerfiles. For more information see:
# https://github.com/miykael/nipype_env

FROM miykael/nipype_level0
MAINTAINER Michael Notter <michaelnotter@hotmail.com>

#---------------------
# Install FSL and ANFI
#---------------------
USER root
RUN apt-get update && \
    apt-get install -y -qq fsl
ENV FSLDIR /usr/share/fsl/5.0
ENV FSLOUTPUTTYPE NIFTI_GZ
ENV FSLMULTIFILEQUIT TRUE
ENV POSSUMDIR /usr/share/fsl/5.0
ENV LD_LIBRARY_PATH /usr/lib/fsl/5.0:$LD_LIBRARY_PATH
ENV FSLTCLSH /usr/bin/tclsh
ENV FSLWISH /usr/bin/wis
ENV PATH /usr/lib/fsl/5.0:$PATH
RUN export PATH FSLDIR FSLMULTIFILEQUIT FSLOUTPUTTYPE

#----------------------------------------
# Clear apt cache and other empty folders
#----------------------------------------
USER root
RUN apt-get autoremove -y && \
    apt-get autoclean -y && \
    apt-get remove -y && \
    apt-get clean -y
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /boot /media /mnt /srv

#-------------------
# Install MATLAB MCR
#-------------------
USER root
RUN mkdir -p /opt
ENV MATLAB_VERSION R2016b
RUN mkdir /opt/mcr_install && \
    mkdir /opt/mcr
RUN wget -q -P /opt/mcr_install http://www.mathworks.com/supportfiles/downloads/${MATLAB_VERSION}/deployment_files/${MATLAB_VERSION}/installers/glnxa64/MCR_${MATLAB_VERSION}_glnxa64_installer.zip
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
RUN export PATH=/opt/mcr/v91/bin:$PATH
RUN export MATLABCMD=/opt/mcr/v91/bin/glnxa64/MATLABWindow

#--------------------------------------
# Install SPM Standalone
#--------------------------------------
USER root
ENV SPM_VERSION 12
ENV SPM_REVISION r6906
ENV SPM_DIR /opt/spm${SPM_VERSION}
ENV SPM_EXEC ${SPM_DIR}/spm${SPM_VERSION}
RUN wget -P /opt http://www.fil.ion.ucl.ac.uk/spm/download/restricted/bids/spm${SPM_VERSION}_${SPM_REVISION}_Linux_${MATLAB_VERSION}.zip
RUN unzip -q /opt/spm${SPM_VERSION}_${SPM_REVISION}_Linux_${MATLAB_VERSION}.zip -d /opt && \
    rm -f /opt/spm${SPM_VERSION}_${SPM_REVISION}_Linux_${MATLAB_VERSION}.zip
RUN ${SPM_EXEC} function exit

RUN export SPM_DIR=/opt/spm12
RUN export SPMMCRCMD='$SPM_DIR/run_spm12.sh /opt/mcr/v91/ script'
RUN export FORCE_SPMMCR=1

#-----------------------------------------
# Source to BASHRC and load configurations
#-----------------------------------------
USER root
RUN ln -snf /bin/bash /bin/sh
RUN ldconfig