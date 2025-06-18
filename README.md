# `__init__.py` 檔案介紹  
## 前言  
這檔案是來把你其他檔案變成裸體的  
假設有個專案長這樣：  
```txt
project/                    ←大包包
│
├── main.py
│
├── utils/                  ←小包包
│   ├── json/               ←迷你包包
│       ├── __init__.py     
│       ├── core.py         
│       ├── settings.py     
│
├── commands/               ←小包包
│   ├── __init__.py         
│   ├── help_handler.py     
│   ├── slash.py            
```  
## 功用  
### 1. 把小包包裡的東西拿出來  
如果你在help_handler定義了一個函式  
```py
def get_help_view()
```  
然後在`__init__.py`裡匯入它：  
```py
# commands/__init__.py
from .help_handler import get_help_view
```  
就像把東西從小包包拿出來放進大包包，這樣你在其他地方就不用再翻小包包  
原本你在`main.py`要這樣寫：  
```py
from commands.help_handler import get_help_view
get_help_view()

# 或是
from commands import help_handler
help_handler.get_help_view()
```
就可以變成：  
```py
from commands import get_help_view
get_help_view()

# 或是
import commands
commands.get_help_view()
```
### 2. `from 小包包 import 迷你包包 as 別名`  
迷你包包可能會放很多檔案  
這時候你可以一次把它們全部丟進小包包  
```py
# utils/json/__init__.py
from .core import init_all_json_files
from .settings import load_all_json_settings
```
這樣就能一次性匯入：  
```py
# commands/slash.py

# 匯入
from utils import json as json_utils
# 使用
json_utils.init_all_json_files()
json_utils.load_all_json_settings()
```
### 3. 限制你從包包裡倒出來的東西（`__all__`）  
它就像種脫膠劑  
只要用了這個，原本東西都黏在迷你包包裡面  
寫到all裡就可以被輕易倒出來  
反之，其他東西會被黏在裡面  
假設：
```py
# utils/json/__init__.py
from .core import init_all_json_files, a
from .settings import load_all_json_settings
__all__ = ["init_all_json_files", "load_all_json_settings"]
```
當你想要一次把迷你包包的東西全部倒出來時：  
```py
# commands/slash.py
from utils.json import * # 只會倒出 __all__ 裡的東西
```
a()會被黏在迷你包包裡，**沒辦法直接使用**  
如果不用＊則**不影響**：  
```py
# commands/slash.py
from utils import json as json_utils
json_utils.a()
# 或是
from utils import json
json.a()
```  
a還是可以用，因為你是直接拿包包裡的東西  
換句話說，「`__all__` 只會限制`from xxx import *`的用法」  
其他則不影響使用  

大概是這樣，希望能幫到你們～  
