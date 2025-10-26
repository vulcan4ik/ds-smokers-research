FROM jupyter/datascience-notebook:latest

COPY requirements.txt requirements.txt

RUN pip install -r requirements.txt

# ==УСТАНАВЛИВАЕМ LSP, BLACK, ISORT ===
RUN pip install --no-cache-dir jupyterlab-lsp 'python-lsp-server[all]'
# ==== ДОБАВЛЯЕМ НАСТРОЙКИ LSP ====
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
# == НАСТРОЙКИ ПОЛЬЗОВАТЕЛЯ ==
WORKDIR /home/jovyan/work
USER ${NB_UID}

# == ЗАПУСК JUPYTER ==
CMD ["start-notebook.sh", "--NotebookApp.token=''"]