# Инструкция по локальной установке библиотеки и сборке приложения

Данное руководство описывает решение задачи по установке C++ библиотеки в пользовательскую директорию (без `sudo`) и настройке окружения.

---

## 1. Подготовка Библиотеки (CMakeLists.txt)
В основной `CMakeLists.txt` библиотеки необходимо добавить инструкции по экспорту. Это создаст "паспорт" библиотеки для CMake.

```cmake
# 1. Установка целей (бинарных файлов)
install(TARGETS math_lib 
        EXPORT MathLibTargets
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        INCLUDES DESTINATION include)

# 2. Установка заголовочных файлов (.h)
install(FILES math_lib.h DESTINATION include)

# 3. Генерация конфига MathLibConfig.cmake для find_package
install(EXPORT MathLibTargets
        FILE MathLibConfig.cmake
        NAMESPACE MathLib::
        DESTINATION lib/cmake/MathLib)
Пояснение к блоку выше:
install(TARGETS ... EXPORT): Говорит CMake, что бинарник math_lib нужно скопировать в папку lib, а его описание сохранить в набор MathLibTargets.

install(FILES ...): Копирует твой заголовочный файл в папку include, чтобы приложение могло сделать #include "math_lib.h".

install(EXPORT ... FILE ...): Самая важная часть. Создает файл MathLibConfig.cmake. Это «паспорт» библиотеки, который ищет команда find_package в приложении.

2. Установка в директорию пользователя
Для установки без прав администратора используем префикс $HOME/.local.

Bash
# В папке с библиотекой:
cmake -B build -DCMAKE_INSTALL_PREFIX=$HOME/.local
cmake --build build
cmake --install build
3. Настройка окружения (~/.bashrc)
Чтобы система видела библиотеку после релогина, добавьте эти строки в конец файла ~/.bashrc:

Bash
# Чтобы CMake (find_package) находил конфиги в твоей папке
export CMAKE_PREFIX_PATH="$HOME/.local:$CMAKE_PREFIX_PATH"

# Чтобы уже собранная программа находила .so файл при запуске
export LD_LIBRARY_PATH="$HOME/.local/lib:$LD_LIBRARY_PATH"
Затем примените изменения: source ~/.bashrc

4. Сборка и запуск приложения
Теперь приложение собирается одной командой, так как все пути прописаны в окружении:

Bash
# В папке для сборки приложения:
cmake /path/to/app/source && cmake --build .

# Запуск:
./app_executable
5. Как отправить изменения в репозиторий (Git)
Когда README.md готов, сохрани его и отправь на сервер:

Bash
git add README.md
git commit -m "Добавлена полная инструкция с пояснениями для защиты"
git push
Описание решения (для защиты перед преподавателем)
Без sudo: Использование $HOME/.local позволяет устанавливать софт локально для текущего пользователя.

find_package: Мы не просто скопировали файлы, а создали конфиг через install(EXPORT). Благодаря CMAKE_PREFIX_PATH команда find_package в приложении отрабатывает успешно.

Runtime: Переменная LD_LIBRARY_PATH нужна, чтобы при запуске готовой программы ОС знала, что искать библиотеку нужно в нашей папке ~/.local/lib. оформи это ридми markdown
