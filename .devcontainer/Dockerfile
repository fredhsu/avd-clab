FROM ghcr.io/aristanetworks/avd/universal:python3.11-avd-devel

ARG _ARTOKEN
ARG _AR_FILE_PATH
ARG _CLAB_VERSION


USER avd

RUN bash -c "$(curl -sL https://get.containerlab.dev)" -- -v ${_CLAB_VERSION} \
    && pip3 install --user yamllint

RUN if [ ! -z ${_ARTOKEN} ]; \
    then \
    ARTOKEN=$(echo -n "${_ARTOKEN}" | base64) \
    && ARSESSION=$(curl -X "POST" "https://www.arista.com/custom_data/api/cvp/getSessionCode/" -H 'Content-Type: application/json'   -d "{\"accessToken\": \"$ARTOKEN\"}" | sed -n 's|.*"session_code":"\([^"]*\)".*|\1|p') \
    && FILE_PATH=${_AR_FILE_PATH} \
    && DOWNLOAD_URL=$(curl -X "POST" "https://www.arista.com/custom_data/api/cvp/getDownloadLink/" -H 'Content-Type: application/json' -d "{\"sessionCode\": \"$ARSESSION\", \"filePath\": \"$FILE_PATH\"}" | sed -n 's|.*"url":"\([^"]*\)".*|\1|p') \
    && mkdir -p /home/avd/tmp \
    && curl "$DOWNLOAD_URL" --output /home/avd/tmp/ceos-lab-temp \
    && docker import /home/avd/tmp/ceos-lab-temp ceos-lab
    ;fi

