# Настройка Ollama с моделью Qwen 2.5:1.5b на CentOS и интеграция через API

Данное руководство поможет вам установить Ollama, настроить модель Qwen 2.5:1.5b, обеспечить доступ через API и включить автоматический запуск с использованием `systemd` на CentOS.

---

## 1. Предварительные требования

### Системные требования
- **Операционная система**: CentOS 7 или более новая.
- **Зависимости**:
  - Docker (если требуется Ollama).
  - Достаточно места на диске для хранения модели.
  - Достаточно оперативной памяти и вычислительных ресурсов для запуска модели.

### Установка необходимых инструментов
Убедитесь, что `curl` установлен для тестирования API:
```bash
sudo yum install -y curl
```

---

## 2. Установка Ollama CLI

### Загрузка и установка
1. Перейдите на [сайт Ollama](https://ollama.com) или на страницу [релизов GitHub](https://github.com/ollama/ollama/releases).
2. Скачайте бинарный файл, подходящий для CentOS.
3. Переместите файл в каталог, указанный в `PATH`:
   ```bash
   sudo mv ollama /usr/local/bin/
   sudo chmod +x /usr/local/bin/ollama
   ```

### Проверка установки
Запустите следующую команду для проверки установки:
```bash
ollama --version
```

---

## 3. Загрузка модели Qwen 2.5:1.5b

Скачайте модель с помощью команды `pull`:
```bash
ollama pull qwen2.5:1.5b
```

Эта команда загрузит модель и сохранит её в локальном хранилище Ollama.

---

## 4. Запуск API-сервера

Для запуска Ollama в режиме API-сервера выполните:
```bash
ollama serve
```
Сервер запустится по умолчанию на `http://localhost:11434`.

### Тестирование сервера
Проверьте состояние сервера:
```bash
curl http://localhost:11434/health
```
Вы должны увидеть подтверждение, что сервер работает.

---

## 5. Использование API

### Пример запроса
Отправьте тестовый запрос для получения ответа от модели:
```bash
curl -X POST http://localhost:11434/api/generate -d '{
  "model": "qwen2.5:1.5b",
  "prompt": "Какова столица Франции?",
  "stream": false
}'
```

### Пример ответа
API вернёт JSON-объект следующего вида:
```json
{
  "model": "qwen2.5:1.5b",
  "response": "Столица Франции - Париж.",
  "done": true
}
```

---

## 6. Настройка автозапуска через systemd

Создайте `systemd`-сервис для автоматического запуска Ollama при старте системы.

### Создание файла сервиса
1. Создайте новый файл:
   ```bash
   sudo nano /etc/systemd/system/ollama.service
   ```
2. Добавьте следующую конфигурацию:
   ```ini
   [Unit]
   Description=Ollama API Server
   After=network.target

   [Service]
   ExecStart=/usr/local/bin/ollama serve
   Restart=always
   User=root
   WorkingDirectory=/root

   [Install]
   WantedBy=multi-user.target
   ```

### Включение и запуск сервиса
1. Обновите конфигурацию systemd:
   ```bash
   sudo systemctl daemon-reload
   ```
2. Включите сервис для автозапуска:
   ```bash
   sudo systemctl enable ollama.service
   ```
3. Запустите сервис:
   ```bash
   sudo systemctl start ollama.service
   ```
4. Проверьте статус сервиса:
   ```bash
   sudo systemctl status ollama.service
   ```

---

## 7. Дополнительные сведения

### Обновление Ollama
Для обновления Ollama CLI скачайте последнюю версию с их [страницы релизов GitHub](https://github.com/ollama/ollama/releases) и замените существующий бинарный файл.

### Логи
Просмотрите журналы сервиса для устранения неполадок:
```bash
sudo journalctl -u ollama.service
```

### Остановка сервиса
Чтобы вручную остановить сервис:
```bash
sudo systemctl stop ollama.service
```

---

## 8. Полезные ссылки
- [Документация API Ollama](https://github.com/ollama/ollama/blob/main/docs/api.md)
- [Репозиторий Ollama на GitHub](https://github.com/ollama/ollama)
- [Руководство по установке Docker](https://docs.docker.com/get-docker/) (если требуется)
