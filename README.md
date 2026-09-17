- ```Shell
  git config --global init.defaultBranch main
  git commit -m 'add README.md'
  git ls-files
  git remote add origin git@github.com: НА ГИТХАБЕ < ИМЯ > /hexlet-git.git
  git branch -M main  
  git push -u origin main  
  git clone git@github.com:<ИМЯ НА ГИТХАБЕ>/hexlet-git.git
  git pull --rebase
  #Флаг --rebase здесь не украшение. 
  #Без него git pull при расхождении историй создает дополнительный коммит слияния,
  #и история проекта обрастает записями, которые ничего не рассказывают о коде.
  #С флагом коммиты просто дописываются в конец локальной истории.
  ```
- ```Shell
  git restore PEOPLE.md #до текущего коммита
  git add PEOPLE.md 
  git commit -m 'remove PEOPLE.md'  
  git rm PEOPLE.md #Равносильно rm + git add
  git checkout e511117979ed453211a1e41ddb0a35810b0c2bda
  git status
  git log
  git diff #сравнение с индексом
  git diff --staged #с HEAD
  ```
- ```Shell
  git show 5120bea3e5528c29f8d1da43731cbe895892eb6d
  git blame INFO.md
  e6f625cf (tirion 2020-09-17 16:14:09 -0400 1) git is awesome!  
  5120bea3 (tirion 2020-09-17 18:04:19 -0400 2) new line  
  git grep line
  INFO.md:new line  
  #Флаг `i` позволяет искать без учета регистра  
  git grep -i hexlet  
  README.md:Hello, Hexlet! How are you?
  ```
- Руками создавать подобный коммит довольно сложно, поэтому в Git добавили команду, автоматизирующую откат. Эта команда называется git revert:
  ```shell
  # Этой команде нужен идентификатор коммита
  # Здесь взят коммит с описанием remove PEOPLE.md
  # Свой хеш посмотрите в выводе git log, он будет другим
  git revert aa600a43cb164408e4ad87d216bc679d097f1a6c
  # После этой команды откроется редактор, ожидающий ввода описания коммита
  # Обычно сообщение revert не меняют, поэтому достаточно просто закрыть редактор
  [main 65a8ef7] Revert "remove PEOPLE.md"
   1 file changed, 1 insertion(+)
   create mode 100644 PEOPLE.md
  # В проект вернулся файл PEOPLE.md
  
  git log -p
  
  commit 65a8ef7fd56c7356dcee35c2d05b4400f4467ca8
  Author: tirion <tirion@got.com>
  Date:   Sat Sep 26 15:32:46 2020 -0400
  
      Revert "remove PEOPLE.md"
  
      This reverts commit aa600a43cb164408e4ad87d216bc679d097f1a6c.
  
  diff --git a/PEOPLE.md b/PEOPLE.md
  new file mode 100644
  index 0000000..cf6db53
  --- /dev/null
  +++ b/PEOPLE.md
  @@ -0,0 +1 @@
  +Haskell Curry
  ```
  Команда git revert может отменять не только последний коммит, но и любой другой коммит из истории проекта. Согласитесь, это очень круто. Без системы контроля версий о таком нельзя было и мечтать.  
  применяет перевернутый diff  
- Git reset
	- ```shell
	  # Добавляем новый коммит, который мы сразу же удалим
	  echo 'test' >> INFO.md
	  git add INFO.md
	  git commit -m 'update INFO.md'
	  
	  [main 17a77cb] update INFO.md
	  1 file changed, 1 insertion(+)
	  # Важно, что мы не делаем git push
	  
	  git reset --hard HEAD~
	  
	  HEAD is now at 65a8ef7 Revert "remove PEOPLE.md"
	  
	  # Если посмотреть `git log`, то последнего коммита там больше нет
	  ```
	- ```shell
	  до reset:                    65a8ef7 ── 17a77cb
	                                              ↑
	                                            HEAD
	  
	  после git reset --hard HEAD~:  65a8ef7
	                                    ↑
	                                  HEAD
	  ```
	- HEAD переехал на предыдущий коммит, а 17a77cb больше не входит в историю. Если бы в команде стояло HEAD~2, то так же исчезли бы два последних коммита.
	- У команды git reset есть множество различных флагов и способов работы. С ее помощью можно удалять коммиты, отменять их без удаления, восстанавливать файлы из истории и так далее. Работа с ней относится к продвинутому использованию Git, но здесь мы затрагиваем только самую базу.
	- Флаг --hard означает полное удаление: вместе с коммитом стираются и сделанные в нем изменения. Без этого флага коммит тоже исчезает из истории, но сами изменения сохраняются: они остаются в рабочей директории, так что с ними можно продолжить работать. Подробнее про HEAD и устройство истории поговорим в уроке про понимание Git.
	- Без --hard команда git reset по умолчанию работает в режиме --mixed, при котором изменения остаются в рабочей директории, но исключаются из индекса (unstage). Затем их можно исправить или отменить и выполнить новый коммит:
	- ```Shell
	  echo 'no code no pain' >> README.md
	  git add README.md
	  git commit -m 'update README.md'
	  
	  [main f85e3a6] update README.md
	   1 file changed, 1 insertion(+)
	  
	  # Теперь откатываем последний коммит
	  git reset HEAD~
	  
	  Unstaged changes after reset:
	  M	README.md
	  
	  git status
	  
	  On branch main
	  Your branch is ahead of 'origin/main' by 1 commit.
	    (use "git push" to publish your local commits)
	  
	  Changes not staged for commit:
	    (use "git add <file>..." to update what will be committed)
	    (use "git restore <file>..." to discard changes in working directory)
	  	modified:   README.md
	  ```
	- Последнего коммита больше не существует. При этом сделанные в нем изменения не пропали. Они находятся в рабочей директории для дальнейшей доработки.
	- Опция --soft позволяет сохранить изменения в индексе:
	- ```Shell
	  # Возвращаем коммит, который только что откатили
	  git add README.md
	  git commit -m 'update README.md'
	  
	  # И откатываем его снова, но теперь с флагом --soft
	  git reset --soft HEAD~1
	  
	  git status
	  
	  On branch main
	  Changes to be committed:
	  (use "git restore --staged <file>..." to unstage)
	  modified: README.md
	  ```
	- Команда git reset --soft отменяет коммит, но оставляет изменения в индексе (staging area). Следующий коммит включит в себя те же изменения, если их не модифицировать.
	- Три режима отличаются ровно одним: куда попадают изменения из удаленного коммита.
	- ```Shell
	  режим                 коммит    изменения остаются
	  --------------------  --------  --------------------------
	  --soft                удален    в индексе
	  --mixed (по умолчанию) удален   в рабочей директории
	  --hard                удален    нигде, изменения стерты
	  ```
	- Отсюда правило выбора. Если коммит нужно просто переоформить, берите --soft: изменения уже подготовлены, достаточно сделать коммит заново. Если правки нужно доработать, берите --mixed. А --hard берите только тогда, когда изменения точно не нужны, потому что вернуть их будет нечем.
- --amend
	- ```Shell
	  echo 'experiment with amend' >> INFO.md
	  echo 'experiment with amend' >> README.md
	  git add INFO.md
	  # Забыли сделать подготовку README.md к коммиту
	  git commit -m 'add content to INFO.md and README.md'
	  
	  [main 256de25] add content to INFO.md and README.md
	   1 file changed, 1 insertion(+)
	  
	  git status
	  
	  On branch main
	  Your branch is ahead of 'origin/main' by 1 commit.
	    (use "git push" to publish your local commits)
	  
	  Changes not staged for commit:
	    (use "git add <file>..." to update what will be committed)
	    (use "git restore <file>..." to discard changes in working directory)
	  	modified:   README.md
	  
	  no changes added to commit (use "git add" and/or "git commit -a")
	  
	  # Увидели, что забыли добавить файл
	  # Добавляем
	  
	  git add README.md
	  git commit --amend
	  # После этой команды откроется редактор, ожидающий ввода описания коммита
	  # Здесь можно поменять сообщение или выйти из редактора, оставив старое
	  
	  [main d96151a] add content to INFO.md and README.md
	   Date: Sat Sep 26 16:02:07 2020 -0400
	   2 files changed, 2 insertions(+)
	  
	  git status
	  
	  On branch main
	  Your branch is ahead of 'origin/main' by 1 commit.
	    (use "git push" to publish your local commits)
	  
	  nothing to commit, working tree clean
	  ```
	- В реальности --amend не добавляет изменения в существующий коммит. Этот флаг откатывает коммит и выполняет новый, с новыми данными. Поэтому мы и видим ровно один коммит, хотя команда git commit выполнялась два раза.
	- Заметно это по хешу: сначала был 256de25, после --amend стал d96151a. Это разные коммиты, а не один исправленный.
	- Применять --amend к последнему коммиту можно любое количество раз, каждый вызов просто заменяет его новым коммитом.
	- Чтобы редактор для ввода описания не открывался, к команде добавляют опцию --no-edit. В этом случае описание коммита не изменится:
- ## Выбор отдельных изменений
  
  Бывает и так, что разные по смыслу правки сделаны в одном файле. Целиком его в коммит отдавать нельзя, а разделять руками долго. Здесь помогает команда git add -p: она показывает измененные куски по одному и спрашивает, брать ли их в индекс.  
	- ```shell
	  git add -p INFO.md
	  
	  diff --git a/INFO.md b/INFO.md
	  index 40f51f1..b0e7c1e 100644
	  --- a/INFO.md
	  +++ b/INFO.md
	  @@ -1,2 +1,3 @@
	  git is awesome!
	  new line
	  +fix typo in docs
	  (1/2) Stage this hunk [y,n,q,a,d,j,J,g,/,e,?]? y
	  ```
	    
	  Отвечать нужно одной буквой. Ответ y кладет кусок в индекс, n пропускает его и оставляет только в рабочей директории, q завершает разбор. Полный список Git печатает по ?.  
	    
	  Тот же режим доступен через интерактивное меню git add -i, где нужно выбрать пункт patch.  
	- git add -i
	- git log --graph
	- ```Shell
	  git branch
	  
	  * main
	  ```
	- Переключимся на момент, когда был выполнен коммит с сообщением *add INFO.md*. Для этого используем команду git switch --detach <хеш коммита>:
	    
	  ```shell
	  git switch --detach e6f625c
	  
	  # Неполный вывод, чтобы не отвлекаться от сути
	  Note: switching to 'e6f625c'.
	  
	  You are in 'detached HEAD' state. You can look around, make experimental
	  changes and commit them, and you can discard any commits you make in this
	  state without impacting any branches by switching back to a branch.
	  
	  Or undo this operation with:
	  
	  git switch -
	  
	  HEAD is now at e6f625c add INFO.md
	  ```
	    
	  Выполните команду выше и изучите рабочую директорию. Обратите внимание, что хеш вашего коммита может отличаться. Вы увидите, что пропала часть изменений из-за возврата в прошлое. Сами изменения никуда не делись, и мы снова можем вернуться на последний коммит следующей командой:  
	    
	  ```shell
	  git switch main
	  ```
	    
	  Здесь main это имя ветки, то есть указателя на последний коммит нашей истории. Флаг --detach в первой команде нужен именно потому, что хеш коммита веткой не является: мы просим Git встать на конкретный коммит, а не на указатель.  
	    
	  Переключившись в нужный коммит, можно не только изучить содержимое репозитория. Еще мы можем забрать какие-то изменения, которые были удалены, но снова понадобились для работы. Для этого достаточно их скопировать, переключиться на последний коммит и вставить в нужный файл.  
	- Самый простой способ узнать место нахождения это вызвать команду git branch. В обычной ситуации, когда мы находимся на последнем коммите, Git покажет такой вывод:
	    
	  ```shell
	  git branch
	  
	  * main
	  ```
	    
	  Звездочка отмечает, где мы находимся. Но если прямо сейчас загружен коммит из прошлого, то вывод станет таким:  
	    
	  ```shell
	  * (HEAD detached at e6f625c)
	    main
	  ```
	- ```GitHub
	  git stash          # спрятать изменения
	  git switch other-branch
	  # ... поработал ...
	  git switch -
	  git stash pop      # вернуть изменения
	  ```
- .gitignore
	- ```GitHub
	  # В этом файле можно оставлять комментарии
	  # Имя файла .gitignore
	  # Файл нужно создать самостоятельно
	  
	  # Каждая строчка это шаблон, по которому происходит игнорирование
	  
	  # Игнорируем файл в любой директории проекта
	  access.log
	  
	  # Игнорируем директорию в любой директории проекта
	  node_modules/
	  
	  # Игнорируем каталог в корне рабочей директории
	  /coverage/
	  ```
- Исключите файлы *notes.txt* и *todo.md* из репозитория гит таким образом, чтобы сами файлы остались в рабочей директории, но любые изменения в них больше не отслеживались гитом.
  Далее добавьте все получающиеся изменения в индекс и сделайте коммит.  
	- ```Shell
	  echo "notes.txt" >> .gitignore
	  echo "todo.md" >> .gitignore
	  git rm --cached notes.txt
	  git rm --cached todo.md
	  git commit -m "qwe" --amend 
	  ```
-
- # Теория: Stash
	- ```Shell
	  touch FILE.md
	  git add FILE.md
	  git status
	  
	  On branch main
	  Your branch is up to date with 'origin/main'.
	  
	  Changes to be committed:
	  (use "git restore --staged <file>..." to unstage)
	  new file: FILE.md
	  
	  # Прячем файлы
	  # После этой команды пропадут все изменения в отслеживаемых файлах
	  # независимо от того, добавлены они в индекс или нет
	  git stash
	  
	  Saved working directory and index state WIP on main: e7bb5e5 update README.md
	  
	  git status
	  
	  On branch main
	  Your branch is up to date with 'origin/main'.
	  
	  nothing to commit, working tree clean
	  ```
	- Команда git stash не удаляет файлы. Они попадают в специальное место внутри директории *.git* на временное хранение. Эта команда не трогает только неотслеживаемые файлы, то есть те, которые вы создали, но еще не добавили в индекс командой git add. Чтобы спрятать и их, понадобится флаг -u: git stash -u.
	- Стэш позволяет сохранить внутрь любое количество изменений, а вот достаются они не в произвольном порядке. Проверим на двух пачках подряд:
	- ```Shell
	  # Прячем первую пачку изменений
	  git stash
	  
	  Saved working directory and index state WIP on main: e7bb5e5 update README.md
	  
	  # Изменяем файлы и прячем вторую пачку
	  git stash
	  
	  Saved working directory and index state WIP on main: e7bb5e5 update README.md
	  
	  # Смотрим, что лежит в стэше
	  git stash list
	  
	  stash@{0}: WIP on main: e7bb5e5 update README.md
	  stash@{1}: WIP on main: e7bb5e5 update README.md
	  
	  # Возвращаем последние изменения, то есть stash@{0}
	  git stash pop
	  
	  Dropped refs/stash@{0} (b896d4a0126ef4409ede63857e5d996953fe75c5)
	  ```
	- Обратите внимание на нумерацию: stash@{0} это последняя спрятанная пачка, а не первая. Именно ее и достает git stash pop, поэтому изменения возвращаются в порядке, обратном тому, в котором прятались. Это и называется стеком: кто зашел последним, выходит первым.
-
-
- # УРА
	- ## я выучил базовый гит
-
-
