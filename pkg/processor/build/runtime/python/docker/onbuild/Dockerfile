# Copyright 2017 The Nuclio Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

ARG NUCLIO_LABEL=latest
ARG NUCLIO_ARCH=amd64

# Supplies python 2 wheels
FROM python:2 as python2-builder

COPY pkg/processor/runtime/python/py/requirements /requirements

RUN pip download \
        -d /whl \
        --exists-action i \
        -r /requirements/python2.txt

# Supplies python 3.6 & common wheels
FROM python:3.6 as python36-builder

COPY pkg/processor/runtime/python/py/requirements /requirements

# Common wheels
RUN pip download \
        --no-binary msgpack \
        -d /whl \
        --exists-action i \
        -r /requirements/common.txt

# Python 3.6 wheels
RUN pip download \
        -d /whl \
        --exists-action i \
        -r /requirements/python3_6.txt

# Supplies processor
FROM quay.io/nuclio/processor:${NUCLIO_LABEL}-${NUCLIO_ARCH} as processor

# Doesn't do anything but hold processor binary and all Python code required to run the handler
FROM scratch

COPY pkg/processor/runtime/python/py /home/nuclio/bin/py

# Copy processor
COPY --from=processor /home/nuclio/bin/processor /home/nuclio/bin/processor

# Copy wheels
COPY --from=python2-builder /whl /home/nuclio/bin/py/whl
COPY --from=python36-builder /whl /home/nuclio/bin/py/whl
