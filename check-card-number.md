## Пример Bash для проверки номеров кредитных карт
Bash-скрипт:

```
#!/bin/bash

# regexp to get a valid credit card number

echo $1 | grep -qE '^([0-9]{4}[- ]?){3}[0-9]{4}$'

if [ $? -eq 0 ]; then
	echo "$1 is a valid credit card number."
else
	echo "$1 is an invalid credit card number."
```

Выполнение скрипта Bash:

$ chmod +x validate_credit_card_number.sh

$ ./validate_credit_card_number.sh "1234 5678 1234 5678"
1234 5678 1234 5678 is a valid credit card number.

$ ./validate_credit_card_number.sh 1234567812345678
1234567812345678 is a valid credit card number.

$ ./validate_credit_card_number.sh 1234-5678-1234-5678
1234-5678-1234-5678 is a valid credit card number.

$ ./validate_credit_card_number.sh 1234-5678-1234-56786
1234-5678-1234-56786 is an invalid credit card number.

$ ./validate_credit_card_number.sh 1234-55678-1234-5678
1234-55678-1234-5678 is an invalid credit card number.

## Пример на языке Python для проверки номеров кредитных карт
Скрипт Python:

```
import re
cc_list=['1234 5678 1234 5678',
'1234567812345678',
'1234-5678-1234-5678',
'1234-5678-1234-56786',
'1234-55678-1234-5678']
pattern = '^([0-9]{4}[- ]?){3}[0-9]{4}$'
for eachnumber in cc_list:
    result = re.match(pattern, eachnumber)
    if result:
        print(eachnumber+" is a valid credit card number.")
    else:
        print(eachnumber+" is an invalid credit card number.")
```

### Выполнение скрипта Python:

$ python3 validate_credit_card_number.py

1234 5678 1234 5678 is a valid credit card number.

1234567812345678 is a valid credit card number.

1234-5678-1234-5678 is a valid credit card number.

1234-5678-1234-56786 is an invalid credit card number.

1234-55678-1234-5678 is an invalid credit card number.
Заключение
В этом руководстве мы узнали, как использовать скрипты Bash или Python для проверки номеров кредитных карт в системах Linux.

Поскольку используется только регулярное выражение, это оказывается довольно простой задачей для любого из этих языков программирования.

И, конечно, многие другие языки программирования способны справиться с этой задачей.
