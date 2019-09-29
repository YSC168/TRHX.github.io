---
title: Selenium 显式等待条件及其含义
tags:
  - 爬虫
  - Selenium
categories: 
  - Python3 学习笔记
  - 学习经验
description: Selenium 显式等待条件及其含义附表
thumbnail: https://cdn.jsdelivr.net/gh/TRHX/ImageHosting/ITRHX-PIC/thumbnail/selenium.png
avatar: https://cdn.jsdelivr.net/gh/TRHX/CDN-for-itrhx.com@2.1.9/images/trhx.png
---

|                       等待条件                  |                     含义                      |
| -------------------------------------------- | ----------------------------------------- |
| title_is                                                        | 标题是某内容                              |
| title_contains                                             | 标题包含某内容                            |
| presence_of_element_located                  | 节点加载出，传入定位元组，如 (By.ID, 'p')   |
| visibility_of_element_located                     | 节点可见，传入定位元组           |
| visibility_of                                                 | 可见，传入节点对象        |
| presence_of_all_elements_located           | 所有节点加载出           |
| text_to_be_present_in_element                | 某个节点文本包含某文字  |
| text_to_be_present_in_element_value     | 某个节点值包含某文字    |
| frame_to_be_available_and_switch_to_it frame | 加载并切换   |
| invisibility_of_element_located               | 节点不可见          |
| element_to_be_clickable                        |  节点可点击         |
| staleness_of                                           |  判断一个节点是否仍在 DOM，可判断页面是否已经刷新   |
|element_to_be_selected                         | 节点可选择，传节点对象   |
|element_located_to_be_selected           | 节点可选择，传入定位元组   |
|element_selection_state_to_be              | 传入节点对象以及状态，相等返回 True，否则返回 False   |
|element_located_selection_state_to_be | 传入定位元组以及状态，相等返回 True，否则返回 False   |
|alert_is_present                                       | 是否出现 Alert      |

更多等待条件极其用法介绍：https://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.support.expected_conditions

Selenium 的使用：https://www.itrhx.com/2019/08/22/A32-Python3-spider-C04/