---
title: "Azure Resource Manager 템플릿 함수 - 숫자 | Microsoft Docs"
description: "Azure Resource Manager 템플릿에서 숫자 작업을 수행하는 데 사용할 수 있는 함수에 대해 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 66984bef9e82df80818eea31bd37de524b567b33
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿용 숫자 함수

Resource Manager는 정수 작업을 위한 다음 함수를 제공합니다.

* [추가](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [min](#min)
* [max](#max)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>추가
`add(operand1, operand2)`

제공된 두 정수의 합을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- | 
|operand1 |예 |int |더할 첫 번째 숫자입니다. |
|operand2 |예 |int |더할 두 번째 숫자입니다. |

### <a name="examples"></a>예

다음 예제에서는 두 개의 매개 변수를 추가합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>반환 값

매개 변수의 합계를 포함하는 정수입니다.

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

반복 루프의 인덱스를 반환합니다. 

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| loopName | 아니요 | string | 반복을 가져오기 위한 루프의 이름입니다. |
| offset |아니요 |int |0부터 시작하는 반복 값에 더할 숫자입니다. |

### <a name="remarks"></a>설명

이 함수는 항상 **copy** 개체에 사용됩니다. **offset** 값을 제공하지 않으면 현재 반복 값이 반환됩니다. 반복 값은 0부터 시작합니다.

**loopName** 속성을 사용하면 copyIndex에서 리소스 반복 또는 속성 반복을 참조하는지 여부를 지정할 수 있습니다. **loopName**에 값을 제공하지 않으면 현재 리소스 종류 반복이 사용됩니다. 속성에서 반복하는 경우 **loopName**의 값을 제공합니다. 
 
**copyIndex**를 사용하는 방법의 설명은 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.

### <a name="examples"></a>예

다음 예제에서는 복사 루프 및 이름에 포함되는 인덱스 값을 보여 줍니다. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>반환 값

반복의 현재 인덱스를 나타내는 정수입니다.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

제공된 두 정수의 나누기를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |int |나누어지는 수입니다. |
| operand2 |예 |int |나누는 데 사용되는 정수입니다. 0일 수 없습니다. |

### <a name="examples"></a>예

다음 예제에서는 다른 매개 변수로 매개 변수 하나를 나눕니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>반환 값

나누기를 나타내는 정수입니다.

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

값을 부동 소수점 숫자로 변환합니다. 논리 앱과 같은 응용 프로그램에 사용자 지정 매개 변수를 전달할 때만 이 함수를 사용합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |문자열 또는 int |부동 소수점 숫자로 변환할 값입니다. |

### <a name="examples"></a>예

다음 예제에서는 float를 사용해서 매개 변수를 논리 앱에 전달하는 방법을 보여 줍니다.

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
        "custom1": {
            "value": "[float('3.0')]"
        },
        "custom2": {
            "value": "[float(3)]"
        },
```

### <a name="return-value"></a>반환 값
부동 소수점 수입니다.

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

지정된 값을 정수로 변환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| valueToConvert |예 |문자열 또는 int |정수로 변환할 값입니다. |

### <a name="examples"></a>예

다음 예제는 사용자가 제공한 매개 변수 값을 정수로 변환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[variables('intValue')]"
        }
    }
}
```

### <a name="return-value"></a>반환 값

정수입니다.

<a id="min" />

## <a name="min"></a>Min
`min (arg1)`

정수 배열 또는 쉼표로 구분된 정수 목록 중에서 최소값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |정수 배열 또는 쉼표로 구분된 정수 목록 |최소값을 가져올 컬렉션입니다. |

### <a name="examples"></a>예

다음 예제에서는 배열 및 정소 목록에 min을 사용하는 방법을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

### <a name="return-value"></a>반환 값

컬렉션의 최소값을 나타내는 정수입니다.

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

정수 배열 또는 쉼표로 구분된 정수 목록 중에서 최대값을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| arg1 |예 |정수 배열 또는 쉼표로 구분된 정수 목록 |최대값을 가져올 컬렉션입니다. |

### <a name="examples"></a>예

다음 예제에서는 배열 및 정소 목록에 max를 사용하는 방법을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

### <a name="return-value"></a>반환 값

컬렉션의 최대값을 나타내는 정수입니다.

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

제공된 두 정수를 사용하여 나누기한 나머지를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |int |나누어지는 수입니다. |
| operand2 |예 |int |나누는 데 사용되는 정수로, 0일 수 없습니다. |

### <a name="examples"></a>예

다음 예제에서는 다른 매개 변수로 매개 변수 하나를 나눈 나머지를 반환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>반환 값
나머지를 나타내는 정수입니다.

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

제공된 두 정수의 곱하기를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |int |곱할 첫 번째 숫자입니다. |
| operand2 |예 |int |곱할 두 번째 숫자입니다. |

### <a name="examples"></a>예

다음 예제에서는 다른 매개 변수로 매개 변수 하나를 곱합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>반환 값

곱하기를 나타내는 정수입니다.

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

제공된 두 정수의 빼기를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| operand1 |예 |int |빼는 피감수입니다. |
| operand2 |예 |int |빼는 감수입니다. |

### <a name="examples"></a>예

다음 예제에서는 다른 매개 변수에서 매개 변수 하나를 뺍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>반환 값
빼기를 나타내는 정수입니다.

## <a name="next-steps"></a>다음 단계
* Azure Resource Manager 템플릿의 섹션에 대한 설명은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 여러 템플릿을 병합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.
* 리소스 유형을 만들 때 지정된 횟수만큼 반복하려면 [Azure 리소스 관리자에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.
* 만든 템플릿을 배포하는 방법을 보려면 [Azure Resource Manager 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.


