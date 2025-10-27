FROM jupyter/datascience-notebook:latest

# Копируем требования и устанавливаем все зависимости (включая JupyterLab, Codeium, LSP, Black)
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
# LSP И РАСШИРЕНИЯ
RUN pip install --no-cache-dir \
    jupyterlab-lsp \
    'python-lsp-server[all]' \
    codeium-jupyter \
    jupyter_black \
    python-lsp-black 

RUN jupyter server extension enable jupyter_lsp --sys-prefix

RUN jupyter labextension enable @jupyterlab/completer-extension:base-service && \
    jupyter labextension enable @jupyterlab/fileeditor-extension:language-server && \
    jupyter labextension enable @jupyterlab/lsp-extension:settings && \
    jupyter labextension enable @jupyterlab/notebook-extension:language-server
# Настройки LSP (включаем pylsp для Python и автоподсказки по мере ввода)
# ✅ НОВОЕ
RUN mkdir -p /home/jovyan/.jupyter/lab/user-settings/@krassowski/jupyterlab-lsp
RUN echo '{\
    "languageServers": {\
    "pylsp": {\
    "serverSettings": {\
    "pylsp": {\
    "plugins": {\
    "pycodestyle": {\
    "enabled": true,\
    "ignore": ["E501"]\
    },\
    "pyflakes": {"enabled": true},\
    "pylint": {"enabled": false},\
    "pydocstyle": {"enabled": false}\
    }\
    }\
    }\
    }\
    },\
    "continuousHinting": true\
    }' > /home/jovyan/.jupyter/lab/user-settings/@krassowski/jupyterlab-lsp/plugin.jupyterlab-settings


# Настройки Codeium (Windsurf) – указываем URL и оставляем токен пустым (пользователь введёт свой)
# RUN mkdir -p /home/jovyan/.jupyter/lab/user-settings/windsurf
# RUN echo '\
#     {\n\
#     "windsurf.enterprise_url": "https://windsurf.dev",\n\
#     "windsurf.auth_token": ""\n\
#     }\n' > /home/jovyan/.jupyter/lab/user-settings/windsurf/settings.json

# Активируем Jupyter Black (форматирование кода) по умолчанию
RUN mkdir -p /home/jovyan/.ipython/profile_default
RUN echo "c.InteractiveShellApp.extensions = ['jupyter_black']" > /home/jovyan/.ipython/profile_default/ipython_config.py

# Рабочая директория
WORKDIR /home/jovyan/work
USER ${NB_UID}

# Запуск JupyterLab (без токена аутентификации)
ENV JUPYTER_ENABLE_LAB=1
CMD ["start-notebook.sh", "--ServerApp.token=''", "--ServerApp.password=''"]





