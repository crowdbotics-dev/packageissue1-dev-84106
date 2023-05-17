FROM crowdbotics/cb-django:3.8-slim-buster AS build

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libcairo2 \
    libpango-1.0-0 \
    libpangocairo-1.0-0 \
    libgdk-pixbuf2.0-0 \
    libffi-dev \
    shared-mime-info

# Copy dependency management files and install app packages to /.venv
COPY ./Pipfile ./Pipfile.lock /
COPY ./modules/ /modules/
RUN PIPENV_VENV_IN_PROJECT=1 pipenv install --deploy


FROM crowdbotics/cb-django:3.8-slim-buster AS release
ARG SECRET_KEY

# Set Working directory
WORKDIR /opt/webapp

# Add runtime user with respective access permissions
RUN groupadd -r django \
  && useradd -d /opt/webapp -r -g django django \
  && chown django:django -R /opt/webapp
USER django

# Copy virtual env from build stage
COPY --chown=django:django --from=build /.venv /.venv
ENV PATH="/.venv/bin:$PATH"

# Copy app source
COPY --chown=django:django . .

# Collect static files and serve app
RUN python3 manage.py collectstatic --no-input
CMD waitress-serve --port=$PORT packageissue1_dev_84106.wsgi:application
