FROM jupyter/datascience-notebook:latest

COPY docker/jupyter/requirements.txt requirements.txt
RUN pip install -r requirements.txt

# == УСТАНАВЛИВАЕМ LSP и CODEIUM ==
RUN pip install --no-cache-dir \
    codeium-jupyter \
    jupyterlab-lsp \
    'python-lsp-server[all]'

# == АКТИВИРУЕМ СЕРВЕРНЫЕ РАСШИРЕНИЯ ==
RUN jupyter server extension enable codeium_jupyter --sys-prefix && \
    jupyter server extension enable jupyter_lsp --sys-prefix

# == АКТИВИРУЕМ LABEXTENSIONS (FRONTEND) ==
RUN jupyter labextension install codeium-jupyter --no-build && \
    jupyter labextension install @krassowski/jupyterlab-lsp --no-build && \
    jupyter lab build

# == НАСТРОЙКИ LSP ==
RUN mkdir -p /home/jovyan/.jupyter/lab/user-settings/@krassowski/jupyterlab-lsp
RUN echo '\
    {\n\
    "languageServers": {\n\
    "pylsp": {\n\
    "version": 2,\n\
    "argv": ["pylsp"],\n\
    "languages": ["python"]\n\
    }\n\
    },\n\
    "continuousHinting": true\n\
    }\n' \
    > /home/jovyan/.jupyter/lab/user-settings/@krassowski/jupyterlab-lsp/plugin.jupyterlab-settings

# == НАСТРОЙКИ WINDSURF ==
RUN mkdir -p /home/jovyan/.jupyter/lab/user-settings/windsurf
RUN echo '\
    {\n\
    "windsurf.enterprise_url": "https://windsurf.dev",\n\
    "windsurf.auth_token": ""\n\
    }\n' \
    > /home/jovyan/.jupyter/lab/user-settings/windsurf/settings.json

# == РАБОЧАЯ ДИРЕКТОРИЯ ==
WORKDIR /home/jovyan/work
USER ${NB_UID}

# == ЗАПУСК JUPYTER ==
CMD ["start-notebook.sh", "--NotebookApp.token=''"]
