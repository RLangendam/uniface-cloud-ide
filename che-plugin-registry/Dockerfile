FROM node:12.22.1
RUN git clone https://github.com/eclipse-che/che-plugin-registry.git --depth 1 --branch 7.29.x
COPY che-editors.yaml che-plugin-registry/che-editors.yaml
CMD cd che-plugin-registry && ./build.sh --organization uniface
