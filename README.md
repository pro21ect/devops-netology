Privet

Файл .gitignore каталога teraform содержит следующие шаблоны игнорирования:

1. Исключаются локальные каталоги terraform
2. Файлы состояния Terraform с описанием текущую развернутую инфраструктуру tfstate
3. Файлы журнала сбоев crash.log
4. Файлы .tfvars с конфиденциальными данными
5. Файлы переопределения override.tf и прочие
6. Файлы конфигурации .terraformrc, terraform.rc
