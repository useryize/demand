最后更新时间：2026-10-22 17:25:32（北京时间，UTC+08:00）

## 需求 1
路由入口：http://localhost:8000/#/drainageSystem/
- 这个入口下的列表页，表格筛选项label现在是左对齐，需要改成右对齐
- 管网信息下的列表表格，第一列太宽了，这个是列表选择操作栏，需要把宽度该小点


## 需求 2
河道入口：/#/drainageSystem/base/riverInfo/detail?pageStatus=add
港渠入口：/#/drainageSystem/base/canal/detail?pageStatus=add

- 河道和港渠的新增页面，空间信息的起点信息终点信息来自地图，点击地图会返回点击的坐标信息

**地图获取信息方式**
```tsx
// 加载独立河流 JSON 文件
const riverFiles = [
  'https://k8stest.cscec3b-iti.com/lifeline-gis/lifeline/wfs?service=WFS&version=1.1.0&request=GetFeature&typeNames=lifeline:RIVER_013_jingjiang&srsName=EPSG:4326&outputFormat=application/json',
 'https://k8stest.cscec3b-iti.com/lifeline-gis/lifeline/wfs?service=WFS&version=1.1.0&request=GetFeature&typeNames=lifeline:RIVER_014_songzi&srsName=EPSG:4326&outputFormat=application/json',
 'https://k8stest.cscec3b-iti.com/lifeline-gis/lifeline/wfs?service=WFS&version=1.1.0&request=GetFeature&typeNames=lifeline:RIVER_015_hudu&srsName=EPSG:4326&outputFormat=application/json',
 'https://k8stest.cscec3b-iti.com/lifeline-gis/lifeline/wfs?service=WFS&version=1.1.0&request=GetFeature&typeNames=lifeline:RIVER_015_hudu&srsName=EPSG:4326&outputFormat=application/json',
 'https://k8stest.cscec3b-iti.com/lifeline-gis/lifeline/wfs?service=WFS&version=1.1.0&request=GetFeature&typeNames=lifeline:RIVER_017_wuhan_hanjiang&srsName=EPSG:4326&outputFormat=application/json',
 'https://k8stest.cscec3b-iti.com/lifeline-gis/lifeline/wfs?service=WFS&version=1.1.0&request=GetFeature&typeNames=lifeline:RIVER_018_fuhe&srsName=EPSG:4326&outputFormat=application/json'
];

for (const file of riverFiles) {
  const resp = await fetch(file);
  const geojson = await resp.json();
  const feat = geojson.features[0];
  console.log(feat.properties.obj_name, feat.properties.id);
}

// 地图渲染，点击高亮并显示 ID
<LifeLinePolygon positions={[ring]} fillColor="#3b82f6" fillOpacity={0.3}
  outlineColor="#2563eb" clampToGround
  onClick={() => handleRiverSelect(feat.properties.id)}
  data={{ id: feat.properties.id, name: feat.properties.obj_name }} />
```


# 需求 3
入口：http://localhost:8000/#/drainageSystem
接口：[档案接口](../src/pages/DrainageSystem/BasicInformation/services/32_前端档案type_code_name总表_20261020_档案接口.md)
档案编码：[档案编码](../src/pages/DrainageSystem/BasicInformation/constants/localArchiveCodes.ts)
- 入口下的档案映射关系对齐档案接口
- 改动点列表筛选项，新增编辑表单的下拉值

# 需求 4
入口：/#/drainageSystem/base/rainGaugeStation/detail?pageStatus=add
- 关联设备需要使用关联组件
  - 描述：关联雨量站现场视频监控点位，通常仅在重点站配置，实时画面及录像回放从视频监控模块获取。
- 关联设备下方新增关联信息 [关联视频（选配）]
  - 描述：关联雨量站现场视频监控点位，通常仅在重点站配置，实时画面及录像回放从视频监控模块获取。

# 需求 5

入口：/#/drainageSystem 下的基础信息详情新增页面。

- 新增保存时，请求体需传入 `areaCode`，类型为字符串。
- 取值规则：有区划时取区划编码 `sysDistrictCode`；无区划时取市州编码 `sysCityCode`。
- 两者均为空时，保留已有 `areaCode`；无已有值则不传。
- 仅新增时按上述规则设置，编辑继续提交原有 `areaCode`，不重新计算。
- 覆盖范围：排水泵站、污水处理厂、调蓄设施、排水口、闸门、排涝水系、河道、河道测站、水库湖泊、港渠、雨量站、排水分区、易涝点、排水户。
- 排水口、闸门当前未开放手工新增入口，仅同步补齐新增服务方法的参数规则。

# 需求 6

入口：/#/drainageSystem/base/waterSystem

- 排涝水系列表上方统计卡片与表格联动，点击统计项后刷新列表并回到第一页。
- 点击卡片时，列表查询的筛选字段和取值必须与列表状态筛选保持一致，不能直接使用卡片展示用的 `key`。
- 状态参数：正常传 `status=NORMAL`，建设中传 `status=CONSTRUCTION`，规划中传 `status=PLANNING`。
- 列表查询与统计查询使用相同筛选条件，统计查询不传分页参数。

# 需求 7

入口：/#/drainageSystem/base/riverInfo/detail，管理信息中的“流经区划”。

- 流经区划不与市州联动，不根据所选市州限制候选范围。
- 选择弹窗按树形展示区划，保留省、市、区县的父子层级，支持展开、收起和独立多选。
- 候选仅展示到区县层级，保留六位行政区划编码，不展示街道及以下节点。
- 公共关联组件通过调用方传入的 `tree` 参数控制树形展示，默认仍为平铺列表，不按页面或关系类型判断。
- 层级候选限制由河道页面通过 `optionFilter` 传入，公共组件不内置河道业务规则。

# 需求 8

入口：/#/drainageSystem/base/riverInfo

- 河道列表上方统计卡片与表格联动，点击统计项后刷新列表并回到第一页。
- 点击卡片时，列表查询的筛选字段和取值必须与列表状态筛选保持一致，不能直接使用卡片展示用的 `key`。
- 状态参数：正常传 `status=NORMAL`，整治中传 `status=RECTIFYING`，断流传 `status=DRY`。
- 列表查询与统计查询使用相同筛选条件，统计查询不传分页参数。

# 需求 9

入口：/#/drainageSystem/base/riverInfo/detail，详情和编辑中的“流经区划”。

- 已选流经区划需同时显示编码和区域名称，如 `420102 · 江岸区`，不能只展示编码。
- 接口仅返回编码时，自动从区划候选数据中补齐名称，无需先打开选择弹窗。
- 保存仍提交原区划编码。

# 需求 10

涉及页面：河道测站、水库湖泊、港渠、雨量站列表。

- 四个列表页左侧增加区域树，展示和交互参考排水泵站列表。
- 复用公共列表组件的区域树，选中区域后通过 `areaCode` 传入列表查询及统计查询，保持两者区域筛选条件一致。
- 切换区域时刷新列表并回到第一页。

# 需求 11

涉及页面：排涝水系、河道、河道测站、港渠、雨量站、污水处理厂、排水户。

- “管理单位”“运行管理单位”统一显示为“主管单位”，“管理单位名称”改为“主管单位名称”，同步调整表单标签、输入提示、列表列名及相关分组标题。
- 新增、编辑表单中的主管单位由文本输入改为组织单选弹窗，复用项目现有 `G3ReferPro` 的 `uc_org` 参照，支持选择和清空。
- 详情、编辑时通过组织 ID 回显组织名称，保存提交组织 ID 字符串，不提交参照对象。
- 保留现有接口字段：排涝水系、河道、河道测站、港渠、雨量站的 `managementUnit` 映射到 `operationManagementUnit`；污水处理厂同步补齐该字段的回填与保存；排水户的 `competentUnit` 映射到 `competentDepartment`。
- 验证情况：定向 ESLint 和差异检查通过；浏览器已确认组织弹窗可打开，当前候选列表为空，选中后的回填及保存流程待有候选数据时联调。

# 需求 12

涉及页面：水库湖泊、雨量站的新增、编辑和详情表单。

- 权属单位由文本输入改为组织单选弹窗，复用与主管单位相同的 `OrganizationSelect` 组件，支持选择、清空和名称回显。
- 保存仍通过原有 `ownershipUnit` 字段提交组织 ID 字符串，不提交参照对象。
- 排水泵站、调蓄设施、排水口、闸门、易涝点的权属单位已有组织选择弹窗，保留现有实现。

# 需求 13

涉及页面：污水处理厂的新增、编辑和详情表单。

- 运营单位名称由文本输入改为单选弹窗，参考 `E:\code\g3-lifeline-warning-process-frontend` 中运营单位使用的 `G3ReferPro`、`uc_department` 部门参照。
- 复用 `OrganizationSelect`，通过 `referCode="uc_department"` 选择部门；主管单位和权属单位继续使用默认的 `uc_org` 组织参照。
- 选择后回显名称，支持清空，保留当前 `operatingUnit` 表单及接口字段，提交所选部门 ID 字符串，不提交参照对象。

# 需求 14

入口：`#/drainageSystem/base/riverMeasureStation/detail`，河道测站的新增、编辑和详情表单。

- 所属河流的河道主键、名称和业务编码分别处理，不得将 `riverId` 直接作为河流名称或编码展示。
- 详情、编辑回显时，关联选择值使用 `riverId`；河流名称优先取 `river.name`，兼容 `belongRiver`；河流编码优先取 `river.code`，兼容 `belongRiverCode`。
- 新增、编辑保存时，通过 `riverId` 提交所选河道的主键字符串，不提交用于展示的 `belongRiver`、`riverCode` 或 `belongRiverCode` 字段。
- 清空所属河流后，不得沿用原有的河道主键。

# 需求 15

入口：`#/drainageSystem/base/riverMeasureStation/detail`，河道测站的新增、编辑和详情表单。

- 站点编码（`billCode`）由后端生成，新增、编辑页面不可手工填写，取消必填校验。
- 新增时提示“保存时自动生成”，详情、编辑时回显后端返回的站点编码。
- 保存时不提交 `billCode` 字段。

# 需求 16

入口：`#/drainageSystem/base/riverMeasureStation/detail`，河道测站的所属河流选择弹窗。

- 弹窗中置顶的已选河流与其他候选保持一致，展示“河流编号 · 河流名称”。
- 已选项匹配到接口候选数据时，优先使用候选的完整标签，不得因沿用详情中的纯名称标签而丢失河流编号。
- 保持原有单选、已选项置顶及通过河道主键关联的行为。

# 需求 17

涉及页面：水库湖泊列表及新增、编辑和详情表单。

- “主管部门”统一更名为“主管单位”，同步调整列表列名、筛选标签和表单标签。
- 新增、编辑表单复用现有 `OrganizationSelect` 组件的 `uc_org` 组织单选弹窗，支持选择、清空及名称回显。
- 保留原有 `competentDepartment` 表单及接口字段，保存提交组织 ID 字符串，不提交参照对象；详情、编辑通过组织 ID 回显名称。

# 需求 18

涉及页面：水库湖泊列表及新增、编辑和详情表单。

- 对齐 `25_前端联调完整字段与JSON附录_20261016(1).md` 中水库湖泊接口契约，编号使用必填的 `code`，不再将兼容旧单据编号 `billCode` 用作新增编号。
- 数值字段提交 JSON number，空数值不转为 0；对齐数值范围、精度及文本长度，保存前校验经纬度同时填写、正常库容不超过总库容。
- 保存仅提交文档白名单字段，关联测站使用 `stationIds`；新增不传 `id`，编辑保留详情返回的 `ts`。

# 需求 19

更新时间：2026-10-22 15:09:40（北京时间，UTC+08:00）

涉及页面：水库湖泊列表的“所属水系”筛选及新增、编辑和详情表单。

- 所属水系取排涝水系列表接口 `/api/v1/admin/drainage/basic-data/water-bodies/water-systems/query`，分页加载候选，展示水系编号和名称。
- 表单和列表筛选共用候选查询方法，统一使用 `G3Select`，支持搜索和清空，提示“请选择所属水系”。
- 表单保存和列表筛选均使用水系主键 `waterSystemId`，不使用写死的水系名称；表单清空归属时提交 `null`，列表清空筛选时不传该条件。
- 表单及列表筛选共用 `waterSystemService.ts` 的 `queryWaterSystemOptions`，每页请求 200 条，按 `total` 逐页加载全部水系后合并展示；接口未返回 `total` 时，以某页不足 200 条作为结束条件，不遗漏后续分页数据。

# 需求 20

更新时间：2026-10-22 15:09:40（北京时间，UTC+08:00）

涉及页面：港渠列表的“所属水系”筛选及新增、编辑和详情表单。

- 所属水系使用排涝水系列表，复用水库湖泊的候选查询方法，分页加载并展示水系编号和名称，支持搜索和清空。
- 表单选择值使用水系主键，保存和列表筛选均映射至接口字段 `waterSystemId`；表单清空归属提交 `null`，列表清空筛选后不传该条件。
- 详情、编辑根据水系主键回显候选名称，列表通过接口水系摘要展示名称。
- 将水系候选查询方法提取至公共服务目录，港渠与水库湖泊共用。
- 表单和列表筛选与水库湖泊采用相同分页策略：每页 200 条，按 `total` 加载全部分页；未返回 `total` 时，某页不足 200 条则停止，全部结果加载完成后展示下拉选项。

# 需求 21

更新时间：2026-10-22 15:15:27（北京时间，UTC+08:00）

涉及页面：港渠新增、编辑和详情表单的管理信息。

- 市州选项通过现有区划接口加载，移除写死的市州数据，展示市州编码及名称，支持搜索；选择和保存使用 `sysCityCode` 六位编码。
- 新增时根据当前用户区划定位默认市州，详情、编辑通过已有市州编码回显。
- 建设年代由数字输入改为年份选择器，按 `YYYY` 展示；服务层转换选择器时间戳，保存 `buildYear` 年份字符串，清空时提交 `null`。

# 需求 22

更新时间：2026-10-22 15:23:23（北京时间，UTC+08:00）

涉及页面：港渠新增、编辑、详情及列表。

- 对齐 `25_前端联调完整字段与JSON附录_20261016(1).md` 的港渠接口白名单，渠道编号统一使用必填字段 `code`，修复表单填写 `billCode` 导致新增请求缺少编号的问题，列表同步展示 `code`。
- 编号最多 128 字符，名称最多 64 字符，起终点地址最多 255 字符；起终点经纬度必填，经度范围 -180 至 180、纬度范围 -90 至 90，小数最多 10 位。
- 长度与设计流量为非负数，整数最多 14 位、小数最多 2 位；渠底、渠顶高程允许负值，整数最多 7 位、小数最多 3 位。
- 保存时将数值输入统一转换为 JSON number，按接口精度处理地图坐标和数值，空数值保留为空，不转成 0。
- 保持已有档案编码、所属水系主键、市州编码、建设年份字符串及关联 ID 集合映射；新增不提交 `id`，编辑保留详情 `ts`，不回传展示字段。

# 需求 23

更新时间：2026-10-22 15:37:07（北京时间，UTC+08:00）

涉及页面：雨量站列表的“权属单位”筛选。

- 权属单位由固定名称下拉改为现有 G3 `referPro` 组织单选弹窗，使用 `uc_org` 参照，支持选择、名称回显及清空。
- 保持原有筛选顺序和展开后显示的规则，提示“请选择权属单位”。
- 列表及统计查询通过 `ownershipUnit` 提交组织 ID，不提交组织编号、名称或参照对象；清空后不传该筛选条件。

# 需求 24

更新时间：2026-10-22 15:41:42（北京时间，UTC+08:00）

涉及页面：雨量站列表的“所属区划”筛选。

- 移除固定区划名称选项，复用现有区划接口 `districtdepartment/selectTopTreeById` 加载区划树，提取六位区县编码的选项，不展示省、市汇总节点及街道以下节点。
- 下拉展示“区划编码 区划名称”，支持搜索和清空，保持原有筛选顺序。
- 列表及统计查询通过 `sysDistrictCode` 提交区划编码字符串；清空后不传该条件，左侧区域树仍通过 `areaCode` 筛选。

# 需求 25

更新时间：2026-10-22 15:53:58（北京时间，UTC+08:00）

范围：排水基础信息 14 个页面中已有的行业主管部门字段，参考旧项目 `E:\code\g3-lifeline-warning-process-frontend\src\pages\Drainage`。

- 旧项目行业主管部门使用 `uc_department` 部门参照；当前复用 `OrganizationSelect`，通过 `referCode="uc_department"` 实现部门单选、名称回显及清空。
- 已有字段涉及雨量站、水库湖泊、闸门、调蓄设施、易涝点及排水户，表单标签统一为“行业主管部门”，不向其余页面额外新增字段。
- 保存沿用 `competentDepartment`，排水户沿用 `competentUnit` 映射至 `competentDepartment`，提交部门 ID 字符串，不提交部门名称、编号或参照对象。
- 水库湖泊、排水户已有的对应列表筛选同步改为 `uc_department` 单选弹窗；查询适配层对部门参照优先提取 `id`，不影响档案项使用 `code`。
- 主管单位（`managementUnit`）、权属单位（`ownershipUnit`）继续使用 `uc_org` 组织参照。本需求更新此前水库湖泊、排水户将 `competentDepartment` 作为组织选择的约定。
- 接口限制：现有排水户查询文档及服务白名单不支持 `competentDepartment` 筛选，该列表筛选需要后端补充查询契约后再接通；表单保存映射已支持该字段。

# 需求 26

更新时间：2026-10-22 16:16:07（北京时间，UTC+08:00）

涉及页面：雨量站新增、编辑和详情。

- 严格对齐 `25_前端联调完整字段与JSON附录_20261016(1).md` 雨量站新增白名单；移除只读 `deviceIds` 的提交，不提交编号、视频、量程、分辨率和其他展示字段。
- 站点编号（`billCode`）改为后台维护的只读展示，取消手工输入和必填校验；站点名称必填且最多 50 字符，地址最多 64 字符。
- 市州、区县均为必填的六位编码，页面和服务层同步校验。
- 建站日期将控件时间戳转换为上海时区 `yyyy-MM-dd` 后提交 `buildTime`，详情日期转换为控件可回显的时间戳，清空时提交 `null`。
- 经度范围 -180 至 180、纬度范围 -90 至 90，小数最多 6 位；报警水位整数最多 14 位、小数最多 2 位，允许负值。数值以 JSON number 提交，空值不转成 0。
- 设备、视频关联以及未提供维护字段的量程、分辨率保留只读展示，防止填写后无法保存；此约定更新此前雨量站关联编辑的需求，维护能力待后端扩展接口。
- 行业主管部门、主管单位及权属单位仍提交参照 ID 字符串；新增不提交 `id`，编辑原样保留详情 `ts`。

# 需求 27

更新时间：2026-10-22 16:26:38（北京时间，UTC+08:00）

涉及页面：排水分区列表的“所属区划”筛选。

- 移除固定区划名称，复用区划接口 `districtdepartment/selectTopTreeById` 加载选项，提取六位区县编码，不展示省、市汇总节点及街道以下节点。
- 下拉展示“区划编码 区划名称”，支持搜索和清空，保持原有筛选顺序。
- 列表及统计查询均通过 `sysDistrictCode` 提交区划编码字符串；清空后不传该条件。
- 查询根节点为 `id: ''`，候选范围以接口返回的区划树为准，当前不额外限制市州。

# 需求 28

更新时间：2026-10-22 16:43:03（北京时间，UTC+08:00）

涉及页面：河道列表、河道新增/编辑/详情表单及河道测站列表。

- 移除河道列表和表单“所属水系”的固定名称选项，复用 `queryWaterSystemOptions` 查询排涝水系列表，按主键 `waterSystemId` 筛选、保存及回显，清空归属提交 `null`。
- 河道列表“所属水系”优先展示接口的 `waterSystemName`，兼容水系摘要名称，避免直接展示主键。
- 移除河道测站列表“所属河流”的固定名称选项，调用河道列表接口加载候选，展示河流编号和名称，筛选字段使用主键 `riverId`。
- 水系、河流候选每页 200 条，按 `total` 加载完整分页；未返回 `total` 时，某页不足 200 条则停止。下拉均支持搜索和清空。
- 列表和统计查询采用相同的主键筛选条件，清空后不传对应条件，避免旧字段被查询白名单忽略。

# 需求 29

更新时间：2026-10-22 16:58:25（北京时间，UTC+08:00）

- 关联信息的关联设备联调
- 路由路口：/#/drainage/deviceBaseInfo
- 老的排水项目：E:\code\g3-lifeline-warning-process-frontend\src\pages\Drainage
- 需要考虑两个入参: 
  - specialType: 'csaqzx_ps' ：表示是排水项目
  - associatedTargetType: 'glmblx0412' ：表示关联的哪个页面类型
- specialType是固定值就用csaqzx_ps
- associatedTargetType需要做常量映射
- 映射值需要参考接口https://k8stest.cscec3b-iti.com/g3-devsupport-web/defdoc/getByDefdoclistCode?defdoclistCode=D001442&keyField=code
- 接口返回数据已由用户提供于 `associatedTargetType.json`，据此维护 `deviceTargetTypes.ts` 的排水专项类型常量及关系映射。
- 设备候选统一使用旧项目 `POST /g3-lifeline-warning-process-web/warndevicebaseinfo/queryList`，参数放请求体，分页使用 `current/pageSize`，读取 `content/totalElements`；每页 200 条，加载完整分页后按设备编号或名称筛选。
- 页面映射：排水泵站（设备、监测设备）`glmblx0408`，污水厂 `glmblx0407`，排水口 `glmblx0416`，河道测站 `glmblx0411`，雨量站 `glmblx0412`，易涝点 `glmblx0413`。
- 当前档案数据未包含闸门类型，闸门设备候选需补齐类型后接通；页面提示缺少类型配置，不发送无类型查询。
- 保留已有业务保存契约：设备候选选择使用设备主键，不改动各页面的设备保存字段；雨量站仍不提供设备绑定写入，不新增视频接口或路由。

# 需求 30

更新时间：2026-10-22 17:11:56（北京时间，UTC+08:00）

涉及页面：基础信息的关联设备候选列表。

- 对齐旧设备管理列表请求体，除 `specialType`、`associatedTargetType` 外，补充已分配条件 `isAllocated: '1'` 和区划 `sysSubdistrictCode`；区划与请求头 `ZONING-CODE` 同取 `storage.getUserInfo()?.district?.code`，不回退到其他缓存区划，为空时不构造无效编码。
- 分页同时提交 `current`、`pageNumber` 和 `pageSize`，后续分页的两个页码保持一致；继续读取 `content/totalElements` 并按设备编号、名称筛选。
- 排查发现旧项目开发代理为 `pre.cscec3b-smx.com`，当前项目为 `test.cscec3b-smx.com`。两者不属于同一环境，当前保留项目代理，设备数据差异需在相同环境下验证。

# 需求 31

更新时间：2026-10-22 17:25:32（北京时间，UTC+08:00）

涉及页面：河湖信息下的排涝水系、河道、河道测站、水库湖泊、港渠、雨量站。

- 各页面业务编码均由后端自动生成，取消编码字段必填校验，不允许手工填写，空值提示统一为“保存时自动生成”。
- 详情及编辑继续回显后端编码，保留原有字段映射；新增无需提供编码即可通过前端表单校验。
- 本需求更新此前按旧接口文档要求水系、河道、水库湖泊、港渠编号手工填写或必填的约定；名称、类型、市州和区划等其他字段的校验保持原有规则。
