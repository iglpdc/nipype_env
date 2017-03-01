# This Dockerfile is based on the dockerfile 'fmriprep' from the Poldrack
# Lab (https://github.com/poldracklab/fmriprep). The jupyter notebook foundation
# is based on jupyter/docker-stacks's base-notebook.
#
# This means that the same copyrights apply to this Dockerfile, as they do for
# the above mentioned dockerfiles. For more information see:
# https://github.com/miykael/nipype_env

FROM miykael/nipype_level0
MAINTAINER Michael Notter <michaelnotter@hotmail.com>

USER root
#-------------------
# Install MATLAB MCR
#-------------------
RUN apt-get update && \
    apt-get install -y -qq --no-install-recommends bc libxp6
# Install Matlab: adapted from the good old install_spm_mcr.sh of @chrisfilo
RUN mkdir -p /opt/mcr_install /opt/mcr && \
    wget -P /opt/mcr_install http://www.fil.ion.ucl.ac.uk/spm/download/restricted/utopia/MCR/glnxa64/MCRInstaller.bin && \
    echo '-P installLocation="/opt/mcr"' > /opt/mcr_install/mcr_options.txt && \
    chmod +x /opt/mcr_install/MCRInstaller.bin && \
    /opt/mcr_install/MCRInstaller.bin -silent -options /opt/mcr_install/mcr_options.txt && \
    rm -rf /opt/mcr_install

#--------------------------------------
# Install SPM Standalone
#--------------------------------------
ENV SPM_VERSION=8 \
    SPM_REVISION=r5236
ENV SPM_DIR /opt/spm${SPM_VERSION}
ENV SPM_EXEC ${SPM_DIR}/spm${SPM_VERSION}_mcr/spm${SPM_VERSION}

RUN wget -P /opt http://www.fil.ion.ucl.ac.uk/spm/download/restricted/utopia/spm8/spm8_r5236.zip && \
    unzip -q /opt/spm${SPM_VERSION}_${SPM_REVISION}.zip -d /opt && \
    rm -f /opt/spm${SPM_VERSION}_${SPM_REVISION}.zip
RUN ${SPM_DIR}/run_spm8.sh /opt/mcr/v713 quit

ENV SPMMCRCMD="/opt/spm8/run_spm8.sh /opt/mcr/v713/ script" \ 
    FORCE_SPMMCR=1
RUN chmod 755 /opt/spm8/run_spm8.sh
RUN chmod 755 ${SPM_EXEC}
RUN chown -R jovyan:users /home/jovyan/.matlab

#---------------------
# Install FSL and ANFI
#---------------------
RUN apt-get update && \
    apt-get install -y -qq --no-install-recommends fsl-core fsl-atlases
ENV FSLDIR=/usr/share/fsl/5.0 \
    FSLOUTPUTTYPE=NIFTI_GZ \
    FSLMULTIFILEQUIT=TRUE \
    POSSUMDIR=/usr/share/fsl/5.0 \
    LD_LIBRARY_PATH=/usr/lib/fsl/5.0:$LD_LIBRARY_PATH \
    FSLTCLSH=/usr/bin/tclsh \
    FSLWISH=/usr/bin/wish \
    PATH=/usr/lib/fsl/5.0:$PATH

#---------------------------
# Install FreeSurfer (v.6.0)
#---------------------------
#USER root
#RUN curl -sSL https://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/6.0.0/freesurfer-Linux-centos6_x86_64-stable-pub-v6.0.0.tar.gz | tar zxv -C /opt \
#    --exclude='freesurfer/trctrain' \
#    --exclude='freesurfer/subjects/fsaverage_sym' \
#    --exclude='freesurfer/subjects/fsaverage3' \
#    --exclude='freesurfer/subjects/fsaverage4' \
#    --exclude='freesurfer/subjects/fsaverage5' \
#    --exclude='freesurfer/subjects/fsaverage6' \
#    --exclude='freesurfer/subjects/cvs_avg35' \
#    --exclude='freesurfer/subjects/cvs_avg35_inMNI152' \
#    --exclude='freesurfer/subjects/bert' \
#    --exclude='freesurfer/subjects/V1_average' \
#    --exclude='freesurfer/average/mult-comp-cor' \
#    --exclude='freesurfer/lib/cuda' \
#    --exclude='freesurfer/lib/qt'
#
#ENV FSL_DIR=/usr/share/fsl/5.0 \
#    OS=Linux \
#    FS_OVERRIDE=0 \
#    FIX_VERTEX_AREA= \
#    FSF_OUTPUT_FORMAT=nii.gz \
#    FREESURFER_HOME=/opt/freesurfer
#ENV SUBJECTS_DIR=$FREESURFER_HOME/subjects \
#    FUNCTIONALS_DIR=$FREESURFER_HOME/sessions \
#    MNI_DIR=$FREESURFER_HOME/mni \
#    LOCAL_DIR=$FREESURFER_HOME/local \
#    FSFAST_HOME=$FREESURFER_HOME/fsfast \
#    MINC_BIN_DIR=$FREESURFER_HOME/mni/bin \
#    MINC_LIB_DIR=$FREESURFER_HOME/mni/lib \
#    MNI_DATAPATH=$FREESURFER_HOME/mni/data \
#    FMRI_ANALYSIS_DIR=$FREESURFER_HOME/fsfast
#ENV PERL5LIB=$MINC_LIB_DIR/perl5/5.8.5 \
#    MNI_PERL5LIB=$MINC_LIB_DIR/perl5/5.8.5 \
#    PATH=$FREESURFER_HOME/bin:$FSFAST_HOME/bin:$FREESURFER_HOME/tktools:$MINC_BIN_DIR:$PATH
#RUN echo "cHJpbnRmICJtaWNoYWVsbm90dGVyQGhvdG1haWwuY29tXG4xNDY4NVxuICpDNG9aVTJpclRmUGNcbiBGU1ByejhxQ0NXNWE2XG4iID4gL29wdC9mcmVlc3VyZmVyL2xpY2Vuc2UudHh0Cg==" | base64 -d | sh

#----------------------------------------
# Clear apt cache and other empty folders
#----------------------------------------
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /boot /media /mnt /srv
