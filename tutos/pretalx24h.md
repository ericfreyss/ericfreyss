# Enabling 24h Time Format and English (UK) in Pretalx

This guide explains how to force a 24-hour time format on a Pretalx instance by adding the `en-gb` (English UK) locale, while preserving all other installed languages.

This method uses a custom Python settings file to avoid modifying the Pretalx source code directly, ensuring your changes persist through future updates.

## 1. Create the Custom Settings File

Create a file named `custom_settings.py` in your Pretalx data directory (e.g., `/var/pretalx/`).

```python
from pretalx.settings import *
from django.utils.translation import gettext_noop

# 1. Inject required metadata for Pretalx
# This prevents KeyError crashes during form rendering
LANGUAGES_INFORMATION.update({
    'en-gb': {
        'code': 'en-gb',
        'natural_name': 'English (UK)',
        'official': 'English (UK)',
        'rtl': False,
        'visible': True,
        'percentage': 100,
    }
})

# 2. Add English (UK) to the existing language list without overwriting others
if not any(lang[0] == 'en-gb' for lang in LANGUAGES):
    LANGUAGES = list(LANGUAGES) + [('en-gb', gettext_noop('English (UK)'))]

# 3. Global 24h time formatting settings
# TIME_FORMAT = 'H:i'
# DATETIME_FORMAT = 'd/m/Y H:i'
# SHORT_DATETIME_FORMAT = 'd/m/Y H:i'
```

Set the correct ownership for the file:
```bash
sudo chown pretalx:pretalx /var/pretalx/custom_settings.py
```

## 2. Systemd Configuration

To tell Pretalx to use this file instead of the default settings, modify the service unit using an override file.

1.  **Open the override editor:**
    ```bash
    sudo systemctl edit pretalx-web.service
    ```
2.  **Add the following environment variables:**
    ```ini
    [Service]
    Environment="PYTHONPATH=/var/pretalx"
    Environment="DJANGO_SETTINGS_MODULE=custom_settings"
    ```

## 3. Apply Changes

Reload the systemd configuration and restart the Pretalx web service:
```bash
sudo systemctl daemon-reload
sudo systemctl restart pretalx-web
```

## 4. UI Configuration

Once the service has restarted:

1.  Log in to your event's administration interface.
2.  Navigate to **Settings** > **General** > **Internationalization**.
3.  Check **English (UK)** in the available languages list.
4.  Set **English (UK)** as the **Default language**.
5.  Save your changes.

## 5. Validation

To verify that the file is correctly loaded by Django, run the following command:
```bash
sudo -u pretalx \
  PRETALX_CONFIG_FILE=/etc/pretalx/pretalx.cfg \
  PYTHONPATH=/var/pretalx \
  DJANGO_SETTINGS_MODULE=custom_settings \
  /var/pretalx/venv/bin/python -m pretalx shell --unsafe-disable-scopes \
  -c "from django.conf import settings; print(settings.TIME_FORMAT)"
```

If the command returns `H:i`, the configuration is active.
