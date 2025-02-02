FROM python:3.12.8-alpine3.21

LABEL maintainer="Hasan Ozgan"

ARG WWWUSER
ARG WWWGROUP
ARG NODE_VERSION=22
ARG MYSQL_CLIENT="mysql-client"
ARG POSTGRES_VERSION=17

WORKDIR /opt/app

ENV TZ=UTC
ENV SUPERVISOR_COMMAND="uvicorn main:app --host 0.0.0.0 --port 8000 --reload"
ENV SUPERVISOR_USER="sail"

# RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# docker run -v .:/opt hasanozgan/yq:latest eval-all '. as $item ireduce ({}; . *+ $item)' /opt/stubs/docker-compose.stub /opt/stubs/pgsql.stub /opt/stubs/mariadb.stub
RUN apk update
RUN apk add --update zsh zsh-autosuggestions zsh-syntax-highlighting bind-tools
RUN apk add --update git
RUN apk add --update make ca-certificates zip unzip supervisor nano openssl binutils bind-tools libcap libpng-dev librsvg ffmpeg gnupg
RUN apk add --update curl wget yq jq xq ripgrep grep sed fzf bat tree vim
RUN apk add --update nodejs npm yarn pnpm
RUN apk add --update postgresql-client mysql-client sqlite libpq-dev

RUN sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
RUN echo "source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ~/.zshrc && \
    echo "source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh" >> ~/.zshrc
RUN echo "source /opt/app/.venv/bin/activate" >> ~/.zshrc

RUN zsh -ic "omz theme set philips"
RUN zsh -ic "omz plugin enable python"
RUN zsh -ic "omz plugin enable pip"
RUN zsh -ic "omz plugin enable pipenv"

# RUN python -m venv /root/.venv
# RUN source /root/.venv/bin/activate
RUN pip install --upgrade pip pip-tools packaging pyproject-api poetry
# RUN pip install "fastapi[standard]" alembic uvicorn typer httpie click
RUN pip install pytest pylint flake8 pycodestyle black isort mypy tox pre-commit # unittest coverage --root-user-action
RUN pip install --upgrade packaging
RUN pip install mycli pgcli litecli iredis # --root-user-action
# RUN echo "Acquire::http::Pipeline-Depth 0;" > /etc/apt/apt.conf.d/99custom && \
#     echo "Acquire::http::No-Cache true;" >> /etc/apt/apt.conf.d/99custom && \
#     echo "Acquire::BrokenProxy    true;" >> /etc/apt/apt.conf.d/99custom

# RUN addgroup --force -g $WWWGROUP sail
# RUN adduser -ms /bin/zsh --no-user-group -g $WWWGROUP -u 1881 sail

RUN mkdir /var/log/supervisor
RUN touch /var/log/supervisor/supervisord.log
RUN addgroup --system --gid ${WWWGROUP} sail
RUN adduser --system --disabled-password --home /home/sail --shell /bin/zsh --uid ${WWWUSER} --ingroup sail sail


COPY start-container /usr/local/bin/start-container
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
RUN chmod +x /usr/local/bin/start-container

EXPOSE 8000/tcp

ENTRYPOINT ["start-container"]
