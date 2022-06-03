Отчет Лаба9
export GITHUB_USERNAME= artemgarshin
export PACKAGE_MANAGER=brew
export GPG_PACKAGE_NAME=gpg
$Создаем переменные

$PACKAGE_MANAGER install xclip
$Устанавливаем утилиту для работы с буфером обмена

alias gsed=sed
alias pbcopy='xclip -selection clipboard'
alias pbpaste='xclip -selection clipboard -o'
$Переопределяем новые названия для команд

Brew install golang
$Устанавливаем утилиту golang

git clone https://github.com/${GITHUB_USERNAME}/lab08 projects/lab09
cd projects/lab09
git remote remove origin
git remote add origin https://github.com/${GITHUB_USERNAME}/lab09

$Клонируем репозиторий 8-ой лабораторной, переопределяем git для новой ветки нового репозитория

$PACKAGE_MANAGER install ${GPG_PACKAGE_NAME}
$Устанавливаем gpg-утилиту (для создания ключей)

gpg --list-secret-keys --keyid-format LONG
gpg --full-generate-key
gpg --list-secret-keys --keyid-format LONG
gpg -K ${GITHUB_USERNAME}

$Создаем ключи, выводим их в консоль

GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep ssb | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
GPG_SEC_KEY_ID=$(gpg --list-secret-keys --keyid-format LONG | grep sec | tail -1 | awk '{print $2}' | awk -F'/' '{print $2}')
$Создаем переменные с информацией о ключах

gpg --armor --export ${GPG_KEY_ID} | pbcopy
$Копируем в буфер обмена ключ для вставки на GitHub
pbpaste
$Выводим его на консоль

$Далее создаем gpg ключ на github

git config user.signingkey ${GPG_SEC_KEY_ID}
git config gpg.program gpg

$Добавляем информацию о ключе в репозиторий

test -r ~/.bash_profile && echo 'export GPG_TTY=$(tty)' >> ~/.bash_profile
$Меняем настройки консоли

cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
cmake --build _build --target package

$Билдим CMake с архивом бинарных файлов

git tag -s v0.1.0.0
$Создаем защищенный тэг (флаг -s)
git tag -v v0.1.0.0
$Выводим информацию о защите тэга (флаг -v)
git show v0.1.0.0
$Выводим информацию о тэге
git push origin master --tags
$Пушим тэги

github-release --version
$Проверяем версию git-релиза

github-release info -u ${GITHUB_USERNAME} -r lab09
$Выводим информацию о репозитории

github-release release \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "libprint" \
    --description "my first release"

$Создаем релиз с параметрами (--user -  логин git, --repo - название репозитория, --tag - параметр тэга, --name - название (имя) релиза, --description - описание)

export PACKAGE_OS=`uname -s` PACKAGE_ARCH=`uname -m`
export PACKAGE_FILENAME=print-${PACKAGE_OS}-${PACKAGE_ARCH}.tar.gz

$Вводим переменные с именем нашей операционной системы, архитектурой процессора и на их основании строим переменную с именем файла

github-release upload \
    --user ${GITHUB_USERNAME} \
    --repo lab09 \
    --tag v0.1.0.0 \
    --name "${PACKAGE_FILENAME}" \
    --file _build/*.tar.gz
$Заливаем в релиз файл, лежащий в build/*.tar.gz и называем его по переменной, созданной выше

github-release info -u ${GITHUB_USERNAME} -r lab09
$Выводим информацию о релизах репозитория

wget https://github.com/${GITHUB_USERNAME}/lab09/releases/download/v0.1.0.0/${PACKAGE_FILENAME}
$Скачиваем наш архив по ссылке репозитория

tar -ztf ${PACKAGE_FILENAME}
$Разархивируем с целью проверки правильности работы
