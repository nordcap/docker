# инструкции Dockerfile
<ul>
<li>FROM — задаёт базовый (родительский) образ.</li>
<li>LABEL — описывает метаданные. Например — сведения о том, кто создал и поддерживает образ.</li>
<li>ENV — устанавливает постоянные переменные среды.</li>
<li>RUN — выполняет команду и создаёт слой образа. Используется для установки в контейнер пакетов.</li>
<li>COPY — копирует в контейнер файлы и папки.</li>
<li>ADD — копирует файлы и папки в контейнер, может распаковывать локальные .tar-файлы.</li>
<li>CMD — описывает команду с аргументами, которую нужно выполнить когда контейнер будет запущен. Аргументы могут быть переопределены при запуске контейнера. В файле может присутствовать лишь одна инструкция CMD.</li>
<li>WORKDIR — задаёт рабочую директорию для следующей инструкции.</li>
<li>ARG — задаёт переменные для передачи Docker во время сборки образа.</li>
<li>ENTRYPOINT — предоставляет команду с аргументами для вызова во время выполнения контейнера. Аргументы не переопределяются.</li>
<li>EXPOSE — указывает на необходимость открыть порт.</li>
<li>VOLUME — создаёт точку монтирования для работы с постоянным хранилищем.</li>

</ul>

# полезные советы

<p>Слои в итоговом образе создают только инструкции FROM, RUN, COPY, и ADD
Другие инструкции что-то настраивают, описывают метаданные, или сообщают Docker о том, что во время выполнения контейнера нужно что-то сделать, например — открыть какой-то порт или выполнить какую-то команду.</p>

<ul>
<li>В одном файле Dockerfile может присутствовать лишь одна инструкция CMD. Если в файле есть несколько таких инструкций, система проигнорирует все кроме последней.</li>
<li>Инструкция CMD может иметь exec-форму. Если в эту инструкцию не входит упоминание исполняемого файла, тогда в файле должна присутствовать инструкция ENTRYPOINT. В таком случае обе эти инструкции должны быть представлены в формате JSON.</li>
<li>Аргументы командной строки, передаваемые docker run, переопределяют аргументы, предоставленные инструкции CMD в Dockerfile.</li>
<li>Лучше устанавливать с помощью WORKDIR абсолютные пути к папкам, а не перемещаться по файловой системе с помощью команд cd в Dockerfile.</li>
<li>Инструкция WORKDIR автоматически создаёт директорию в том случае, если она не существует.</li>
<li>Можно использовать несколько инструкций WORKDIR. Если таким инструкциям предоставляются относительные пути, то каждая из них меняет текущую рабочую директорию.</li>
<li>Если при каждом запуске контейнера нужно выполнять одну и ту же команду — используйте ENTRYPOINT.</li>
<li>Если контейнер будет использоваться в роли приложения — используйте ENTRYPOINT.</li>
<li>Если вы знаете, что при запуске контейнера вам понадобится передавать ему аргументы, которые могут перезаписывать аргументы, указанные в Dockerfile, используйте CMD.</li>
<p>
<code>
# Задаём значение по умолчанию для переменной
ARG my_var=my_default_value
# Настраиваем команду, которая должна быть запущена в контейнере во время его выполнения
ENTRYPOINT ["python", "./app/my_script.py", "my_var"]
</code>
</p>

<li>Инструкция EXPOSE указывает на то, какие порты планируется открыть для того, чтобы через них можно было бы связаться с работающим контейнером. Эта инструкция не открывает порты. Она, скорее, играет роль документации к образу, средством общения того, кто собирает образ, и того, кто запускает контейнер.

Для того чтобы открыть порт (или порты) и настроить перенаправление портов, нужно выполнить команду docker run с ключом -p. Если использовать ключ в виде -P (с заглавной буквой P), то открыты будут все порты, указанные в инструкции EXPOSE.</li>
<li>Кэширование можно отключить, передав ключ --no-cache=True команде docker build.</li>
<li>Если вы собираетесь вносить изменения в инструкции Dockerfile, тогда каждый слой, созданный инструкциями, идущими после изменённых, будет достаточно часто собираться повторно, без использования кэша. Для того чтобы воспользоваться преимуществами кэширования, помещайте инструкции, вероятность изменения которых высока, как можно ближе к концу Dockerfile.</li>
<li>Объединяйте команды RUN apt-get update и apt-get install в цепочки для того, чтобы исключить проблемы, связанные с неправильным использованием кэша.</li>
<li></li>
<li></li>
</ul>

# Размеры образов image
<ul>
<li>Для того чтобы выяснить примерный размер выполняющегося контейнера, можно использовать команду вида docker container ls -s.</li>
<li>Команда docker image ls выводит размеры образов.</li>
<li>Узнать размеры промежуточных образов, из которых собран некий образ, можно с помощью команды docker image history my_image:my_tag.</li>
<li>Команда docker image inspect my_image:tag позволяет узнать подробные сведения об образе, в том числе — размер каждого его слоя. Слои немного отличаются от промежуточных образов, из которых состоит готовый образ, но, в большинстве случаев их можно рассматривать как одинаковые сущности.</li>
</ul>

# Рекомендации по уменьшению размеров образов и ускорению процесса их сборки

<ol>
<li>Используйте всегда, когда это возможно, официальные образы в качестве базовых образов. Официальные образы регулярно обновляются, они безопаснее неофициальных образов.</li>
<li>Для того чтобы собирать как можно более компактные образы, пользуйтесь базовыми образами, основанными на Alpine Linux.</li>
<li>Если вы пользуетесь apt, комбинируйте в одной инструкции RUN команды apt-get update и apt-get install. Кроме того, объединяйте в одну инструкцию команды установки пакетов. Перечисляйте пакеты в алфавитном порядке на нескольких строках, разделяя список символами \. Например, это может выглядеть так:
<p>
<code>
RUN apt-get update && apt-get install -y \
    package-one \
    package-two \
    package-three
 && rm -rf /var/lib/apt/lists/*
 </code>
 </p>
Этот метод позволяет сократить число слоёв, которые должны быть добавлены в образ, и помогает поддерживать код файла в приличном виде.
</li>
<li>Включайте конструкцию вида && rm -rf /var/lib/apt/lists/* в конец инструкции RUN, используемой для установки пакетов. Это позволит очистить кэш apt и приведёт к тому, что он не будет сохраняться в слое, сформированном командой RUN.</li>
<li>Разумно пользуйтесь возможностями кэширования, размещая в Dockerfile команды, вероятность изменения которых высока, ближе к концу файла.</li>
<li>Пользуйтесь файлом .dockerignore.</li>
<li>Взгляните на dive — отличный инструмент для исследования образов Docker, который помогает в деле уменьшения их размеров.</li>
<li>Не устанавливайте в образы пакеты, без которых можно обойтись.</li>
</ol>


# Команды для управления контейнерами

<code>docker container my_command</code>

<ul>
<li>
<li>create — создание контейнера из образа.</li>
<li>start — запуск существующего контейнера.</li>
<li>run — создание контейнера и его запуск.</li>
<li>ls — вывод списка работающих контейнеров.</li>
<li>inspect — вывод подробной информации о контейнере.</li>
<li>logs — вывод логов.</li>
<li>stop — остановка работающего контейнера с отправкой главному процессу контейнера сигнала SIGTERM, и, через некоторое время, SIGKILL.</li>
<li>kill — остановка работающего контейнера с отправкой главному процессу контейнера сигнала SIGKILL.</li>
<li>rm — удаление остановленного контейнера.</li>
</ul>


# Команды для управления образами

<code>docker image my_command</code>

<ul>
<li>build — сборка образа.</li>
<li>push — отправка образа в удалённый реестр.</li>
<li>ls — вывод списка образов.</li>
<li>history — вывод сведений о слоях образа.</li>
<li>inspect — вывод подробной информации об образе, в том числе — сведений о слоях.</li>
<li>rm — удаление образа.</li>
</ul>


# Разные команды
<ul>
<li>docker version — вывод сведений о версиях клиента и сервера Docker.</li>
<li>docker login — вход в реестр Docker.</li>
<li>docker system prune — удаление неиспользуемых контейнеров, сетей и образов, которым не назначено имя и тег.</li>
</ul>




---------------------------------------------------------------
<p>docker run -it --rm --name my-running-app my-python-app</p>