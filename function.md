# 项目结构说明

## 目录树

```
WHY-CounterTool-dev/
├── assets/
│   └── instrument-types.js        # 仪表资源库（ISA 5.1 标准类型、连接方式映射）
├── css/
│   ├── base.css                   # 基础样式
│   ├── bottom-bar.css             # 底部工具栏样式
│   ├── context-menu.css           # 右键菜单样式
│   ├── drop-hint.css              # 拖放提示样式
│   ├── inspector.css              # Inspector 属性面板样式
│   ├── preview.css                # 预览窗口样式
│   ├── responsive.css             # 响应式布局样式
│   ├── session-banner.css         # 会话恢复横幅样式
│   ├── settings.css               # 设置面板样式
│   ├── toast.css                  # Toast 消息提示样式
│   ├── top-panel.css              # 顶部面板样式
│   └── type-dock.css              # 类型停靠栏样式
├── js/
│   ├── autosave.js                # 自动保存与恢复模块
│   ├── config.js                  # 全局配置常量
│   ├── events.js                  # 事件处理与交互逻辑模块
│   ├── excel-config.js            # Excel 列定义配置模块
│   ├── export-excel.js            # Excel 导出功能模块
│   ├── export-pdf.js              # PDF 导出功能模块
│   ├── history.js                 # 历史记录与撤销/重做模块
│   ├── inspector.js               # Inspector 属性面板模块
│   ├── log.js                     # 操作日志系统
│   ├── main.js                    # 应用主入口模块
│   ├── markers.js                 # 标记管理模块
│   ├── pdf-loader.js              # PDF 导入与处理模块
│   ├── preview.js                 # 预览窗口模块
│   ├── render.js                  # Canvas 渲染模块
│   ├── state.js                   # 全局状态管理
│   ├── types.js                   # 仪表类型管理
│   ├── ui.js                      # 用户界面交互模块
│   └── utils.js                   # 工具函数模块
├── lib/
│   ├── fontawesome/
│   │   ├── webfonts/              # FontAwesome 字体文件
│   │   ├── .gitkeep
│   │   └── all.min.css            # FontAwesome 样式
│   ├── exceljs.min.js             # ExcelJS 库
│   ├── pdf-lib.min.js             # PDF-Lib 库
│   ├── pdf.min.js                 # PDF.js 库
│   └── pdf.worker.min.js          # PDF.js Worker
└── index.html                     # 主页面入口
```

---

## 文件功能及函数清单

### assets/instrument-types.js — 仪表资源库

定义 ISA 5.1 标准仪表类型（100+ 代号）、连接方式映射和工具函数。

**函数：**
- `hasConnectionKeyword(s)` — 检测文本是否已包含连接方式关键字（ANSI/NPT/FLANGED 等）
- `getConnectionSuffix(abbr)` — 根据仪表代号获取连接后缀
- `findInstrumentByAbbr(abbr)` — 从资源库按代号精确查找仪表类型定义

**常量：**
- `INSTRUMENT_TYPES` — 标准仪表类型数组
- `DEFAULT_CONNECTION` — 默认连接方式 `{ suffix: 'ANSI 150# RF' }`
- `SIZE_CONNECTIONS` — 特殊仪表代号 → 连接后缀映射

---

### js/autosave.js — 自动保存与恢复模块

负责将标记数据序列化到 localStorage，支持会话恢复和自动保存防抖。

**函数：**
- `serializeMarkersForDoc(docId)` — 将指定文档的所有标记序列化为可持久化的对象数组
- `buildAutosavePayload()` — 构建完整的自动保存数据负载
- `scheduleAutosave()` — 安排自动保存（带防抖）
- `clearAutosave()` — 清除 localStorage 中的自动保存数据
- `checkPendingRestore()` — 检查是否存在待恢复的上次会话数据
- `restoreSession(data)` — 恢复上次会话数据

---

### js/config.js — 全局配置常量

定义 PDF 渲染、缩放、标记、自动保存等所有可配置参数。

**常量：**
- `PDFJS_VERSION` — PDF.js 库版本号
- `PDFJS_CDN`, `PDFJS_WORKER_CDN`, `PDFLIB_CDN`, `EXCELJS_CDN` — 外部库 CDN 路径
- `PAGE_GAP`, `PAGE_CAPTION_H`, `DOC_GAP` — 画布布局参数
- `MIN_ZOOM`, `MAX_ZOOM`, `ZOOM_SENSITIVITY`, `WHEEL_PAN_SPEED` — 缩放参数
- `MARKER_MIN_DIST`, `MAX_MARKER_NUMBER` — 标记参数
- `AUTOSAVE_KEY`, `AUTOSAVE_DEBOUNCE_MS`, `SETTINGS_KEY` — 自动保存参数
- `defaultSettings` — 默认设置对象
- `isMobile` — 移动端检测
- `MOBILE_RADIUS`, `DESKTOP_RADIUS`, `MOBILE_FONT_SIZE`, `DESKTOP_FONT_SIZE` — 标记尺寸参数
- `PDF_RENDER_SCALE`, `RENDER_MAX_DIM`, `RENDER_MAX_PIXELS` — PDF 渲染参数
- `DESKTOP_LINE_WIDTH`, `MOBILE_LINE_WIDTH` — 线条宽度参数

---

### js/events.js — 事件处理与交互逻辑模块

负责处理鼠标/触摸/键盘事件、标记右键属性面板、设置面板操作、仪表属性管理及导出功能的事件绑定。

**函数：**
- `_fillMarkerContextMenuFields(marker)` — 填充右键菜单表单字段
- `showMarkerContextMenu(screenX, screenY, marker)` — 显示标记右键属性面板
- `hideMarkerContextMenu()` — 隐藏标记右键属性面板
- `getEventPos(e)` — 获取事件相对于画布内容的坐标
- `handleCanvasTap(vx, vy)` — 处理画布点击/轻触事件
- `saveMarkerContextMenu()` — 保存标记右键属性面板的更改
- `updateCaptionSubVisibility()` — 根据"显示页脚"开关更新子设置显隐
- `openCustomAttrManage()` — 打开仪表属性管理对话框
- `closeCustomAttrManage()` — 关闭仪表属性管理对话框
- `refreshInspectorIfOpen()` — 刷新 Inspector（如果已打开）
- `renderCfAttrList()` — 渲染仪表属性管理列表
- `addCustomAttrFromDialog()` — 从对话框添加自定义属性定义

---

### js/excel-config.js — Excel 列定义配置模块

集中管理所有表格的列定义、自定义字段、内置属性状态和列绑定。

**函数：**
- `getDefaultColumns(sheetName)` — 获取指定工作表的默认列定义
- `loadColumnSettings()` — 加载用户列选择配置
- `saveColumnSettings(settings)` — 保存用户列选择配置
- `getEnabledColumns(sheetName)` — 获取某张表启用的列定义列表
- `getExcelColumnDefs(sheetName)` — 获取某张表的 Excel 列定义
- `getSheetColumnsWithCustom(sheetName)` — 获取包含自定义属性的完整列定义
- `getColumnIndex(sheetName, key)` — 获取指定列的实际索引
- `getCustomAttrDefs()` — 获取自定义 marker 属性定义列表
- `saveCustomAttrDefs(defs)` — 保存自定义 marker 属性定义
- `addCustomAttrDef(label, description)` — 添加自定义 marker 属性定义
- `updateCustomAttrDef(key, updates)` — 更新自定义 marker 属性定义
- `removeCustomAttrDef(key)` — 删除自定义 marker 属性定义
- `getCustomAttrValue(marker, attrKey)` — 获取 marker 上的自定义属性值
- `setCustomAttrValue(marker, attrKey, value)` — 设置 marker 上的自定义属性值
- `getAllBindableFields()` — 获取所有可用于列绑定的字段
- `getCustomAttrColumns()` — 获取已启用的自定义属性对应的列定义
- `getBuiltinAttrState()` — 获取内置属性状态
- `saveBuiltinAttrState(state)` — 保存内置属性状态
- `isBuiltinAttrEnabled(key)` — 判断内置属性是否启用
- `isBuiltinAttrHidden(key)` — 判断内置属性是否隐藏
- `updateBuiltinAttrState(key, updates)` — 更新单个内置属性状态
- `restoreAllBuiltinAttrs()` — 恢复所有隐藏的内置属性
- `getVisibleBuiltinAttrs()` — 获取可见的内置属性列表
- `hasHiddenBuiltinAttrs()` — 是否有隐藏的内置属性
- `getCustomTables()` — 获取自定义表格列表
- `saveCustomTables(tables)` — 保存自定义表格列表
- `addCustomTable(name, columns)` — 添加自定义表格
- `removeCustomTable(id)` — 删除自定义表格
- `getColumnBindings()` — 获取列绑定列表
- `saveColumnBindings(bindings)` — 保存列绑定列表
- `addColumnBinding(name, bindField)` — 添加列绑定
- `removeColumnBinding(id)` — 删除列绑定
- `updateColumnBinding(id, updates)` — 更新列绑定
- `getBoundColumnDefs()` — 获取绑定的列定义

**常量：**
- `COLUMN_DEFS` — 列定义配置对象
- `ALL_MARKER_ATTRIBUTES` — 内置属性定义数组
- `MARKER_FIELD_OPTIONS` — 内置属性选项数组

---

### js/export-excel.js — Excel 导出功能模块

生成仪表统计表格（By File、Type Summary、Detail List 和自定义表格）。

**函数：**
- `styleHeaderRow(row)` — 设置表头行样式
- `measureTextWidth(text)` — 估算文本显示宽度
- `autoFitColumns(ws, extraHeaders)` — 自动调整工作表所有列宽
- `estimateRowHeight(ws, row)` — 按当前列宽估算数据行所需行高
- `applyTableFormat(ws)` — 统一应用表格格式
- `downloadExcelBuffer(buffer, filename)` — 下载 Excel 文件到本地
- `exportExcel()` — 导出 Excel 文件入口
- `exportBoth()` — 同时导出 Excel 和 PDF
- `exportExcelCore()` — Excel 导出核心逻辑
- `addCustomTableSheets(wb, sorted)` — 导出用户创建的自定义表格工作表

---

### js/export-pdf.js — PDF 导出功能模块

将标注的标记渲染到 PDF 图纸上并导出。

**函数：**
- `buildMarkedPdfDoc()` — 构建带标注的 PDF 文档
- `exportMarkedPDF()` — 导出带标注的 PDF 文件
- `exportMarkedPDFCore()` — PDF 导出核心逻辑

---

### js/history.js — 历史记录与撤销/重做模块

负责标记的增删改历史记录管理，支持 undo/redo。

**函数：**
- `removeMarkerFromArray(marker)` — 从标记数组中移除指定标记并释放编号
- `insertMarkerToArray(marker)` — 将标记插入到数组末尾并占用编号
- `pushHistory(entry)` — 将操作记录推入历史栈
- `applyHistoryUpdate(marker, entry, toOld)` — 对单个标记应用/恢复字段更新
- `applyHistoryBulkUpdate(marker, entry, toOld)` — 批量更新/恢复标记的多个字段
- `undo()` — 撤销最近一次操作
- `redo()` — 重做最近一次被撤销的操作
- `deleteMarker(marker)` — 删除指定标记
- `findMarkerAtVirtual(vx, vy)` — 在虚拟坐标处查找最近的标记
- `updateUndoButtonState()` — 更新撤销/重做按钮的禁用状态

---

### js/inspector.js — Inspector 属性面板模块

类似 Unity/Godot 的 Inspector，停靠在屏幕右侧，显示选中标记的完整属性。

**函数：**
- `openInspector(marker)` — 打开 Inspector 面板
- `closeInspector()` — 关闭 Inspector 面板
- `toggleInspector()` — 切换 Inspector 面板
- `renderInspector()` — 渲染 Inspector 面板内容
- `getInspectorFieldValue(marker, fieldDef)` — 获取字段值
- `bindInspectorEvents()` — 绑定 Inspector 面板内部事件
- `addInspectorCustomAttr()` — 从 Inspector 面板添加自定义属性
- `renameInspectorCustomAttr(key)` — 重命名自定义属性
- `deleteInspectorCustomAttr(key)` — 删除自定义属性
- `saveInspector()` — 保存 Inspector 面板中的修改
- `inspectorNavigateTo(delta)` — 导航到前一个/后一个标记

---

### js/log.js — 操作日志系统

所有用户操作记录到屏幕右下角，显示 3 条，旧条目 5 秒后自动消失。

**函数：**
- `addLog(msg)` — 添加一条操作日志
- `_removeEntry(id)` — 移除指定日志条目
- `_renderLog()` — 渲染日志到屏幕右下角
- `clearLog()` — 清空日志

---

### js/main.js — 应用主入口模块

初始化画布、绑定 DOM 元素引用、设置响应式画布尺寸，启动应用初始化流程。

**函数：**
- `resizeCanvas()` — 根据窗口尺寸调整画布大小

---

### js/markers.js — 标记管理模块

负责标记的创建、位置校验、编号分配以及插入到标记数组。

**函数：**
- `addMarker(vx, vy)` — 在虚拟坐标处添加一个标记

---

### js/pdf-loader.js — PDF 导入与处理模块

负责 PDF 文件的导入、页面渲染为位图、会话恢复、自动适配视口缩放与平移。

**函数：**
- `importPDF(files)` — 导入 PDF 文件
- `restoreMarkers(data)` — 恢复上次会话的标记数据
- `fitToContent()` — 自动适配视口

---

### js/preview.js — 预览窗口模块

读取内存中的 markers/documents/markerTypes 数据，渲染为多工作表预览表格。

**函数：**
- `pvEscape(s)` — HTML 转义
- `pvCell(text, cls)` — 构造普通表格单元格 HTML
- `pvEditCell(marker, field, text, isCustomAttr)` — 构造可编辑单元格 HTML
- `pvRow(cells, cls)` — 构造表格行 HTML
- `pvGetUniqueTypes()` — 获取 markers 中出现的所有唯一仪表类型
- `pvTypeIdsChanged()` — 检测类型列表是否发生变化
- `pvRenderTypeTabs()` — 构建动态类型标签页按钮
- `pvRenderTypeSheets()` — 构建动态类型对应的 sheet 面板
- `pvRenderTypeDetail(typeId)` — 渲染某个类型的过滤明细表
- `pvSortedMarkers()` — 对 markers 按创建顺序排序
- `pvRenderDetail()` — 渲染 Detail List 详细列表
- `pvFindMarkerById(id)` — 根据 ID 查找 marker 对象
- `pvCommitCell(td)` — 提交单元格编辑
- `pvSetupEditableTables()` — 设置可编辑表格的事件委托
- `pvUpdateBatchBar()` — 更新批量编辑工具栏状态
- `pvPopulateBatchFields(sheetName)` — 填充批量编辑字段下拉选项
- `pvApplyBatch()` — 执行批量修改
- `pvCancelBatch()` — 取消批量选择
- `pvRerenderCurrentSheet()` — 重新渲染当前激活的工作表
- `pvSetupBatchCheckboxes()` — 设置批量编辑复选框的事件委托
- `pvRenderCellByCol(col, marker, index)` — 根据列定义生成单元格 HTML
- `pvLocateMarker(m)` — 定位到图纸上的标记位置
- `pvSetupLocateButtons()` — 设置定位按钮的点击事件委托
- `pvRenderSummary()` — 渲染 Summary 汇总表
- `pvSetupTabs()` — 设置 Tab 切换的事件委托
- `pvSwitchToTab(sheetName)` — 切换到指定工作表标签
- `renderAllTables()` — 渲染所有内置工作表
- `renderPreview()` — 渲染预览窗口主内容
- `openPreview()` — 打开预览窗口
- `closePreview()` — 关闭预览窗口
- `pvSetupColumnBinding()` — 设置列绑定功能
- `pvPopulateBindFields()` — 填充绑定对话框的字段下拉选项
- `pvOpenBindDialog()` — 打开列绑定对话框
- `pvCloseBindDialog()` — 关闭列绑定对话框
- `pvRenderManageList()` — 渲染管理对话框中的绑定列表
- `pvOpenManageDialog()` — 打开管理对话框
- `pvCloseManageDialog()` — 关闭管理对话框
- `pvRenameBinding(id, currentName)` — 重命名绑定列
- `pvRefreshPreview()` — 刷新预览

---

### js/render.js — Canvas 渲染模块

在画布上绘制 PDF 页面、标记圆圈、统计面板等所有可视化内容。

**函数：**
- `flashLocate(marker)` — 定位闪烁动画
- `requestRender()` — 请求渲染（合并多次请求）
- `render()` — 主渲染函数
- `drawPageCaption(page, dx, dy, dw, dh)` — 绘制页面说明文字
- `drawMarker(ctx, m, globalIndex)` — 绘制标记圆圈
- `roundRect(ctx, x, y, width, height, radius)` — 绘制圆角矩形（填充）
- `strokeRoundRect(ctx, x, y, width, height, radius)` — 绘制圆角矩形（描边）
- `updateUI()` — 更新界面显示

---

### js/state.js — 全局状态管理

管理画布平移/缩放、文档/页面列表、标记数据、编号系统、擦除模式等核心状态。

**函数：**
- `getUsedSet()` — 获取已使用编号的集合引用
- `isNumberUsed(num)` — 检查编号是否已被使用
- `reserveNumber(num)` — 将编号标记为已使用
- `releaseNumber(num)` — 释放编号
- `findNextNumber()` — 查找下一个可用编号
- `updateNumberInputState()` — 更新编号输入框的验证状态
- `syncNumberInput()` — 将编号输入框同步为当前下一个可用编号

---

### js/types.js — 仪表类型管理

管理标记类型（仪表类型）的创建、删除、切换。

**函数：**
- `buildDefaultTypes()` — 从资源库加载默认可见类型
- `getTypeById(id)` — 根据 ID 查找类型
- `getCurrentType()` — 获取当前选中类型
- `renderTypeChips()` — 渲染类型选择器 chip 按钮列表
- `deleteType(typeId)` — 删除指定类型
- `selectType(typeId)` — 切换当前选中类型
- `normalizeQuotes(s)` — 将中文引号统一转为英文引号
- `addCustomType()` — 添加自定义仪表类型

---

### js/ui.js — 用户界面交互模块

提供 Toast 消息提示、全局 Tooltip、工具栏提示初始化、清空所有数据等 UI 操作函数。

**函数：**
- `showToast(msg, spinner)` — 显示 Toast 消息提示
- `hideToast()` — 隐藏 Toast 消息提示
- `showTooltip(target)` — 显示全局工具提示
- `hideTooltip()` — 隐藏全局工具提示
- `initTooltips()` — 初始化工具栏按钮的工具提示
- `clearAll()` — 清空所有数据

---

### js/utils.js — 工具函数模块

提供各种通用工具函数。

**函数：**
- `loadScript(src)` — 动态加载外部脚本
- `loadPdfJs()` — 加载 PDF.js 库
- `loadPdfLib()` — 加载 PDF-Lib 库
- `loadExcelJS()` — 加载 ExcelJS 库
- `loadSettings()` — 从 localStorage 加载设置
- `saveSettings()` — 保存设置到 localStorage
- `formatMarkerNumber(n)` — 格式化标记编号（补零）
- `getMarkerTagNumber(m)` — 获取标记的编号显示文本
- `formatMarkerLabel(m)` — 格式化标记标签（如 "PI-001"）
- `formatSizeNote(s)` — 格式化口径显示
- `buildProcessConnection(m)` — 拼接 Process Connection 字符串
- `computeRenderScale(origWidth, origHeight)` — 计算 PDF 渲染缩放比例
- `virtualToScreen(vx, vy)` — 虚拟坐标转屏幕坐标
- `screenToVirtual(sx, sy)` — 屏幕坐标转虚拟坐标
- `escapeHtml(str)` — HTML 转义
- `getDocFileName(docId)` — 获取文档文件名
- `getDetailListIndexMap()` — 获取 Detail List 排序序号映射
- `hexToRgb(hex)` — 十六进制颜色转 RGB 对象
- `normalizeHexColor(hex)` — 规范化十六进制颜色值
- `calculateDistance(p1, p2)` — 计算两点距离
- `pixelsToDrawingMM(pixels)` — 像素转绘图毫米
- `runExportTask(buttons, taskFn, busyMsg, doneMsg, failMsg)` — 执行导出任务（带加载状态）
- `calculatePolygonArea(points)` — 计算多边形面积
- `showPromptDialog(title, defaultValue, placeholder)` — 显示自定义输入对话框