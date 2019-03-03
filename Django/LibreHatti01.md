Python – 3.5.2   
Django – 2.0   
mysql Ver 14.14 Distrib 5.7.24   
Source – https://github.com/GreatDevelopers/LibreHatti/tree/d2.0p3.6   

--- 
   
Today, I am trying to install [LibreHatti](https://github.com/GreatDevelopers/LibreHatti/), upgraded to Django2.0, without importing sample database as we did in [previous blog post](https://hacksj4u.wordpress.com/2019/01/03/installing-django-app-librehatti-2/).   
   
* After cloning and seeting up LibreHatti, I executed command **```python manage.py makemigrations```** and got following error:   
   
    File "/home/suraj/AutoLib/LibreHatti/src/librehatti/urls.py", line 7, in <module>
        from .reports import views as reports_views
    File "/home/suraj/AutoLib/LibreHatti/src/librehatti/reports/views.py", line 7, in <module>
        from .forms import ClientForm
    File "/home/suraj/AutoLib/LibreHatti/src/librehatti/reports/forms.py", line 95, in <module>
        class AddConstraints(forms.Form):
    File "/home/suraj/AutoLib/LibreHatti/src/librehatti/reports/forms.py", line 121, in AddConstraints
        session_start, session_end)]

    django.db.utils.ProgrammingError: (1146, "Table 'suraj_libauto.voucher_financialsession' doesn't exist")   
   
* After reading above, the error is because Table voucher_financialsession is accessed before it is actually created in file "src/librehatti/reports/forms.py".
* So, I modified this file at line 119 as:

    \- session_choices = [('', '--------')] + [(id, str(start) + '-To-' + \
    \-     str(end)) for id, start, end in zip(session_id, \
    \-     session_start, session_end)]
    \+ try:
    \+     session_choices = [('', '--------')] + [(id, str(start) + '-To-' + \
    \+         str(end)) for id, start, end in zip(session_id, \
    \+         session_start, session_end)]
    \+ except:
    \+     session_choices = [('', '--------')]

* You can also use below commands to perform above task:
```
sed -i "119i try:" src/librehatti/reports/forms.py   
sed -i "123i except:" src/librehatti/reports/forms.py   
sed -i "124i session_choices = [('', '--------')]" src/librehatti/reports/forms.py   
sed -i "119,123s/^/    /g" src/librehatti/reports/forms.py   
ed -i "125i\ " src/librehatti/reports/forms.py
sed -i "124s/^/        /g" src/librehatti/reports/forms.py   
```
* Now, again after executing command **```python manage.py makemigrations```** I got below similar error:
    File "/home/suraj/AutoLib/LibreHatti/src/librehatti/reports/forms.py", line 95, in <module>
    class AddConstraints(forms.Form):
    File "/home/suraj/AutoLib/LibreHatti/src/librehatti/reports/forms.py", line 135, in AddConstraints
       zip(mode_of_payment_id, mode)]
    
    django.db.utils.ProgrammingError: (1146, "Table 'suraj_libauto.catalog_modeofpayment' doesn't exist")

* This can be solved by executing following commands:
```
sed -i "134i try:" src/librehatti/reports/forms.py
sed -i "137i except:" src/librehatti/reports/forms.py
sed -i "138i mode_choices = [('', '--------')]" src/librehatti/reports/forms.py
sed -i "139i\ " src/librehatti/reports/forms.py
sed -i "134,137s/^/    /g" src/librehatti/reports/forms.py
sed -i "138s/^/        /g" src/librehatti/reports/forms.py
```

* Again, I executed **```python manage.py makemigrations```** and it successfully executed and returned **No changes detected**.

* Then, I executed **```python manage.py migrate```** and it also executed successfully.

* Now, I executed **```python manage.py makemigrations voucher```** and it successfully created migrations for 'voucher' app.

* When I executed **```python manage.py migrate```** then it gives below error:
    django.db.utils.IntegrityError: (1215, 'Cannot add foreign key constraint')

To be continue...

