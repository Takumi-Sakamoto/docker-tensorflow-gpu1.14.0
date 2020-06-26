# Use an official nvidia runtime as a parent image
FROM tensorflow:base

# CMD ["bash"]

SHELL ["/bin/bash", "-c"]

# nvidia-container-runtime for nvidia-docker2
ENV NVIDIA_VISIBLE_DEVICES ${NVIDIA_VISIBLE_DEVICES:-all}
ENV NVIDIA_DRIVER_CAPABILITIES ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics

# RUN apt update

# this is to be able to use graphics from the container
# Replace 1000 with your user / group id (if needed)
RUN export uid=1000 gid=1000 && \
  mkdir -p /home/developer && \
  mkdir -p /etc/sudoers.d && \
  echo "developer:x:${uid}:${gid}:Developer,,,:/home/developer:/bin/bash" >> /etc/passwd && \
  echo "developer:x:${uid}:" >> /etc/group && \
  echo "developer ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/developer && \
  chmod 0440 /etc/sudoers.d/developer && \
  chown ${uid}:${gid} -R /home/developer && \
  adduser developer sudo

# Set the working directory to $HOME/bonnetal
ENV HOME /home/developer
WORKDIR $HOME

RUN git clone git://github.com/yyuu/pyenv.git $HOME/.pyenv

# ownership of directory
RUN chown -R developer:developer $HOME/.pyenv
RUN chmod 755 $HOME/.pyenv

# user stuff (and env variables)
USER developer
RUN cp /etc/skel/.bashrc $HOME/ && \
  echo 'export NO_AT_BRIDGE=1' >> $HOME/.bashrc && \
  # echo 'export PYENV_ROOT="$HOME/.pyenv"' >> $HOME/.bashrc && \
  # echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> $HOME/.bashrc && \
  echo 'eval "$(pyenv init -)"' >> $HOME/.bashrc

ENV PYENV_ROOT $HOME/.pyenv
ENV PATH $PYENV_ROOT/bin:$PATH

RUN pyenv install 3.6.9 && pyenv global 3.6.9
RUN $HOME/.pyenv/versions/3.6.9/bin/pip install gast==0.2.2 tensorflow-gpu==1.14.0 numpy keras==2.2.4 pandas opencv-python ipython notebook matplotlib tqdm lxml scikit-learn hmmlearn
RUN $HOME/.pyenv/versions/3.6.9/bin/pip install livelossplot category_encoders
RUN $HOME/.pyenv/versions/3.6.9/bin/pip install scikit-image

# Japanese setting (https://qiita.com/cclef/items/dc8692d8b5a0c8c7812c)
RUN echo 'export LC_CTYPE=C.UTF-8' >> $HOME/.bashrc

RUN echo 'export TF_FORCE_GPU_ALLOW_GROWTH=true' >> $HOME/.bashrc

ENTRYPOINT ["/bin/bash","-c"]
