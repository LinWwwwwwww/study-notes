# cppcc笔记
## 零碎知识点

* IDEA快捷键
```
Ctrl+F:页内匹配字段
Ctrl+H:全局匹配字段
Ctrl+Shift+R:全局匹配文件
```

* 设置默认时区
```java
TimeZone.setDefault(TimeZone.getTimeZone("Asia/Shanghai"));
```

* 自定义资源映射
```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/File/**")
        .addResourceLocations("file:/File/");

    registry.addResourceHandler("/static/**")
    	.addResourceLocations("classpath:/static/");

    registry.addResourceHandler("/logs/**")
	.addResourceLocations("file:/logs/");
}
```

* SpringBoot设置默认首页
```java
public void addViewControllers(ViewControllerRegistry registry) {  
	WebMvcConfigurer.super.addViewControllers(registry);
    registry.addViewController("/").setViewName("forward:/Login");  
    registry.setOrder(Ordered.HIGHEST_PRECEDENCE);  
}  
```

* $.post() 和 $.get()都是异步请求的方式，有时候不得不用，但又要同步请求:
```JavaScript
//设置为同步
 $.ajaxSettings.async = false;
  $.post("url", data, function(result) {
   // 请求处理
  },"json");
  //设置回异步
 $.ajaxSettings.async = true;
```
使用了同步后`$.ajaxSettings.async = false;`，及时释放掉，使用异步`$.ajaxSettings.async = true;`,不然会锁死资源，使得其他线程不能访问数据；

* 暂时无法得知main/index.html中184行`<span id="loginName"></span>`如何键入用户名
>已解决

## EasyUI
* 组件为abc，控制abc的语法：
```javascript
$("jQuery选择器获取到abc").abc({
	属性名:值,
	事件:function([参数]){}
})
```

* 组件为abc，控制abc方法的语法：
```javascript
$("jQuery选择器获取到abc").abc("方法名");//调用方法
$("jQuery选择器获取到abc").abc("方法名","参数");//调用方法
```

## 进度

### 理解index页面

* 登录页面理解完成
* 上方状态栏理解完成
* 以上所说的键入用户名应该是在easyui.js中设置，待理解
* 左侧导航栏理解中：通过初始化命令后在mainController中调出内容并返回给easyui.js，后处理数据（待看）
> 用户名的键入与左侧导航栏的内容均通过在easyui.js中初始化，通过login登录成功后所设置的session信息设置userName与其中roleId选择显示的导航信息

* 中间内容块通过角色id进行塞选后显示出，通过easyui传地址，本质与ajax请求差不多
* 表面理解完成再理解内部的详细页

* 不清楚MainController.java中89行resultMap.put("treeList", cacheDataService.jsonMenu(roleId));中的jsonMenu

### 理解内部的详细页

```mssql
select p.id,p.sid,p.`name`,p.url,r.id as rid,p.type, (case when r.id is null then 0 else 1 end) AS isSystem 
FROM sys_permission p 
LEFT JOIN ( 
	SELECT * FROM sys_permission_role WHERE roleId = 1 ) as r 
	ON p.id = r.permissionId  
WHERE p.isSystem = '1' and p.type != '4' 
```

结果：

1		系统设置		1	1	1
2		提案管理		115	1	1
3		稿件管理		219	1	1
4		稿件分析		269	1	1
5		履职管理		1861	1	1
6		委员管理		3071	1	1
7	1	权限管理	SysPermission/index	2	1	1
8	7	新增	SysPermission/insert	3	2	1
9	7	修改	SysPermission/update	4	2	1
10	7	删除	SysPermission/updateIsSystem	5	2	1
11	7	详细	SysPermission/ListTree	6	2	1
12	7	编辑	SysPermission/edit	7	2	1
13	1	角色管理	SysRole/index 	9	1	1
14	13	新增	SysRole/insert	10	2	1
15	13	修改	SysRole/update	11	2	1
16	13	删除	SysRole/updateIsSystem	12	2	1
17	13	详细	SysRole/detial	13	2	1
18	13	编辑	SysRole/edit	14	2	1
19	1	角色授权	SysPermissionRole/index	16	1	1
20	19	角色授权	SysPermissionRole/update	17	2	1
21	19	修改状态	SysPermissionRole/updateIsSystem	18	2	1
22	1	组别管理	SysGroup/index	21	1	1
23	22	新增	SysGroup/insert	22	2	1
24	22	修改	SysGroup/update	23	2	1
25	22	删除	SysGroup/updateIsSystem	24	2	1
26	22	详细	SysGroup/detial	25	2	1
27	22	编辑	SysGroup/edit	26	2	1
28	1	界别管理	SysCircles/index	28	1	1
29	28	新增	SysCircles/insert	29	2	1
30	28	修改	SysCircles/update	30	2	1
31	28	删除	SysCircles/updateIsSystem	31	2	1
32	28	详细	SysCircles/detial	32	2	1
33	28	编辑	SysCircles/edit	33	2	1
34	1	所属专委会管理	SysSpecialcommittee/index	34	1	1
35	34	新增	SysSpecialcommittee/insert	35	2	1
36	34	修改	SysSpecialcommittee/update	36	2	1
37	34	删除	SysSpecialcommittee/updateIsSystem	37	2	1
38	34	详细	SysSpecialcommittee/detial	38	2	1
39	34	编辑	SysSpecialcommittee/edit	39	2	1
40	1	所属单位及职务管理	SysUnitduty/index	41	1	1
41	40	新增	SysUnitduty/insert	42	2	1
42	40	修改	SysUnitduty/update	43	2	1
43	40	删除	SysUnitduty/updateIsSystem	44	2	1
44	40	详细	SysUnitduty/detial	45	2	1
45	40	编辑	SysUnitduty/edit	46	2	1
46	1	履职项目类型	TLvzhiProjectType/index	47	1	1
47	46	新增	TLvzhiProjectType/insert	48	2	1
48	46	修改	TLvzhiProjectType/update	49	2	1
49	46	删除	TLvzhiProjectType/updateIsSystem	50	2	1
50	46	详细	TLvzhiProjectType/detial	51	2	1
51	46	编辑	TLvzhiProjectType/edit	52	2	1
52	1	履职项目管理	LvzhiPerformanceProject/index	53	1	1
53	52	新增	LvzhiPerformanceProject/insert	54	2	1
54	52	修改	LvzhiPerformanceProject/update	55	2	1
55	52	删除	LvzhiPerformanceProject/updateIsSystem	56	2	1
56	52	详细	LvzhiPerformanceProject/detial	57	2	1
57	52	编辑	LvzhiPerformanceProject/edit	58	2	1
58	6	委员管理	SysUser/index	3072	1	1
59	58	新增	SysUser/insert	3073	2	1
60	58	修改	SysUser/update	3074	2	1
61	58	删除	SysUser/updateIsSystem	3075	2	1
62	58	详细	SysUser/detial	3076	2	1
63	58	编辑	SysUser/editVo	3077	2	1
275	1	党派管理	SysParty/index	60	1	1
276	275	新增	SysParty/insert	61	2	1
277	275	修改	SysParty/update	62	2	1
278	275	删除	SysParty/updateIsSystem	63	2	1
279	275	详细	SysParty/detial	64	2	1
280	275	编辑	SysParty/edit	65	2	1
281	1	信息类别管理	SqmyCategory/index	67	1	1
282	281	新增	SqmyCategory/insert	68	2	1
283	281	修改	SqmyCategory/update	69	2	1
284	281	删除	SqmyCategory/updateIsSystem	70	2	1
285	281	详细	SqmyCategory/detial	71	2	1
286	281	编辑	SqmyCategory/edit	72	2	1
287	3	社情民意（拟稿）	SqmyPaper/index	220	1	1
288	287	新增稿件	SqmyPaper/update	221	2	1
289	287	修改	SqmyPaper/update	222	2	1
290	287	删除	SqmyPaper/delete	223	2	1
291	287	详细	SqmyPaper/editVo	224	2	1
292	287	编辑	SqmyPaper/editVo	225	2	1
293	2	我的提案	TaMyProposal/index	116	1	1
294	299	新增	TaProposalPeriod/insertPeriod	125	2	1
295	299	修改	TaProposalPeriod/setIsPeriod	126	2	1
296	299	删除	TaMyProposal/updateIsSystem	127	2	1
297	299	详细	TaMyProposal/detial	128	2	1
298	299	编辑	TaProposalPeriod/selectPeriodById	129	2	1
299	2	会议时间设置	TaProposalPeriod/index	124	1	1
300	305	新增	TaProposalReview/firstView	132	2	1
301	305	修改	TaProposalPeriod/update	133	2	1
302	305	删除	TaProposalPeriod/updateIsSystem	134	2	1
303	305	编辑	TaProposalExport/exportReview-proposal	135	2	1
304	305	编辑	TaProposalExport/exportProposal	136	2	1
305	2	待审查提案	TaProposalReview/index	131	1	1
306	293	新增	TaMyProposal/update	117	2	1
307	293	修改	TaMyProposal/SelectProposalById	118	2	1
308	293	删除	TaMyProposal/deleteProposal	119	2	1
309	293	详细	detial	120	2	1
310	293	编辑	SelectProposalById	121	2	1
311	2	提案初审	TaProposalReview/FirstReviewIndex	138	1	1
312	311	新增	TaProposalReview/FirstReviewIndexinsert	139	2	1
313	311	修改	TaProposalReview/finalView	140	2	1
314	311	删除	TaProposalReview/FirstReviewIndexupdateIsSystem	141	2	1
315	311	编辑（导出）	aProposalExport/exportFinalReview-proposal	142	2	1
316	311	编辑	TaProposalExport/exportProposal	143	2	1
317	2	立案提案	TaProposalRegister/registerProposalIndex	146	1	1
318	317	新增	TaProposalRegister/insert	147	2	1
319	317	编辑	TaProposalRegister/isImportant	148	2	1
320	317	删除	TaProposalRegister/updateIsSystem	149	2	1
321	317	详细	TaProposalRegister/detial	150	2	1
322	317	编辑	TaProposalExport/export-current-register-proposal	151	2	1
323	2	立案提案	TaProposalRegister/committeeRegisterProposalIndex	154	1	1
324	323	新增	TaProposalRegister/insert	155	2	1
325	323	修改	TaProposalRegister/update	156	2	1
326	323	删除	TaProposalRegister/updateIsSystem	157	2	1
327	323	详细	TaProposalReview/SelectProposalById	158	2	1
328	323	编辑	TaProposalExport/exportNotRegister-proposal	159	2	1
329	2	不立案提案	TaNotRegister/index	161	1	1
330	329	新增	TaNotRegister/insert	162	2	1
331	329	修改	TaNotRegister/update	163	2	1
332	329	删除	TaNotRegister/updateIsSystem	164	2	1
333	329	详细	TaProposalReview/SelectProposalById	165	2	1
334	329	编辑	TaNotRegister/edit	166	2	1
335	2	退案处理	TaReturnProposal/index	169	1	1
336	335	新增	TaReturnProposal/insert	170	2	1
337	335	修改	TaReturnProposal/update	171	2	1
338	335	删除	TaReturnProposal/updateIsSystem	172	2	1
339	335	详细	TaReturnProposal/detial	173	2	1
340	335	编辑	TaReturnProposal/cancelProposal	174	2	1
341	2	提案目录	TaProposalPeriod/directoryIndex	177	1	1
342	365	新增	insert	178	2	1
343	365	修改	update	179	2	1
344	365	删除	updateIsSystem	180	2	1
345	365	详细	detial	181	2	1
346	365	编辑	edit	182	2	1
347	2	提案统计	TaStatistics/taStatisticsIndex	184	1	1
348	347	新增	insert	185	2	1
349	347	修改	update	186	2	1
350	347	删除	updateIsSystem	187	2	1
351	347	详细	detial	188	2	1
352	347	编辑	edit	189	2	1
353	2	资料上传	proposalUpload/UploadIndex	190	1	1
354	353	新增	proposalUpload/UploadData	191	2	1
355	353	修改	update	192	2	1
356	353	删除	proposalUpload/DeleteUploadDataById	193	2	1
357	353	详细	detial	194	2	1
358	353	编辑	edit	195	2	1
359	2	通知及学习资料	proposalUpload/studyDataIndex	197	1	1
360	359	新增	insert	198	2	1
361	359	修改	update	199	2	1
362	359	删除	updateIsSystem	200	2	1
363	359	详细	detial	201	2	1
364	359	编辑	proposalUpload/downloadData	202	2	1
365	2	提案分析	TaStatistics/taAnalysisIndex	204	1	1
366	893	新增	insert	205	2	1
367	893	修改	update	206	2	1
368	893	删除	updateIsSystem	207	2	1
369	893	详细	TaProposalHistory/listHistoryProposalByPeriod	208	2	1
370	893	编辑	TaProposalExport/getHistoryProposalByPeriod	209	2	1
371	2	历史数据上传	proposalUpload/UploadHistoryIndex	211	1	1
372	371	新增	insert	212	2	1
373	371	修改	update	213	2	1
374	371	删除	updateIsSystem	214	2	1
375	371	详细	detial	215	2	1
376	371	编辑	edit	216	2	1
377	3	稿件处理	SqmyPaper/approval	229	1	1
378	377	新增常用意见	SqmyOpinion/update	230	2	1
379	377	稿件审批	SqmyApproval/handlePaper	231	2	1
380	377	稿件采用	SqmyPaper/adoptPaper	232	2	1
381	377	稿件查看	SqmyPaper/editVoWord	233	2	1
382	377	稿件编辑	SqmyPaper/saveWords	234	2	1
383	3	上级稿件反馈	SqmyApproval/feedback	241	1	1
384	383	稿件查看	SqmyPaper/editVoWord	242	2	1
386	383	查看呈批表	SqmyApproval/listApproval	243	2	1
389	3	稿件编印	SqmyPaper/printPaper	247	1	1
390	389	列表	SqmyPaper/list	248	2	1
391	389	稿件查看	SqmyPaper/editVoWord	249	2	1
392	389	查看呈批表	SqmyApproval/listApproval	250	2	1
393	389	稿件编辑	SqmyPaper/saveWords	251	2	1
394	389	稿件编印	SqmyBatchable/update	252	2	1
395	3	稿件登分	SqmyPaper/scorePaper	254	1	1
396	395	稿件查看	SqmyPaper/editVoWord	255	2	1
397	395	稿件编辑	SqmyPaper/saveWords	256	2	1
398	395	查看呈批表	SqmyApproval/listApproval	257	2	1
399	395	稿件登分详情	SqmyReward/editVo	258	2	1
400	395	稿件登分	SqmyReward/update	259	2	1
401	3	稿件调度	SqmyApproval/scheduling	261	1	1
402	401	稿件查看	SqmyPaper/editVoWord	262	2	1
403	401	稿件编辑	SqmyPaper/saveWords	263	2	1
404	401	稿件调度	SqmyApproval/paperDispatch	264	2	1
405	401	列表	SqmyApproval/listDispatchPaper	265	2	1
407	4	稿件查询	SqmyPaper/selectPaper	270	1	1
408	407	新增	insert	271	2	1
409	407	修改	update	272	2	1
410	407	删除	updateIsSystem	273	2	1
411	407	详细	detial	274	2	1
412	407	编辑	test/wordNtk	275	2	1
413	4	查看他人拟稿	SqmyPaper/otherPaper	276	1	1
414	413	新增	insert	277	2	1
415	413	修改	update	278	2	1
416	413	删除	updateIsSystem	279	2	1
417	413	详细	detial	280	2	1
418	413	编辑	edit	281	2	1
419	4	查看本次提案	TaProposalHistory/viewOtherProposalIndex	282	1	1
420	419	新增	insert	283	2	1
421	419	修改	update	284	2	1
422	419	删除	updateIsSystem	285	2	1
423	419	详细	detial	286	2	1
424	419	编辑	TaMyProposal/SelectProposalById	287	2	1
425	4	单位界别统计	paperStatistics/sectorStatistics	289	1	1
426	425	新增	insert	290	2	1
427	425	修改	update	291	2	1
428	425	删除	updateIsSystem	292	2	1
429	425	详细	paperStatistics/listPaperBySectorId	293	2	1
430	425	编辑	paperStatistics/exportSectorStatistics	294	2	1
431	4	单位界别统计图	paperStatistics/sectorStatisticsTable	297	1	1
432	431	新增	insert	298	2	1
433	431	修改	update	299	2	1
434	431	删除	updateIsSystem	300	2	1
435	431	详细	detial	301	2	1
436	431	编辑	edit	302	2	1
437	4	信息类别统计图	paperStatistics/infoCategory	304	1	1
438	437	新增	insert	305	2	1
439	437	修改	update	306	2	1
440	437	删除	updateIsSystem	307	2	1
441	437	详细	detial	308	2	1
442	437	编辑	edit	309	2	1
443	4	委员信息员稿件统计	paperStatistics/committeeAndMessenger	311	1	1
444	443	新增	insert	312	2	1
445	443	修改	update	313	2	1
446	443	删除	updateIsSystem	314	2	1
447	443	详细	detial	315	2	1
448	443	编辑	edit	316	2	1
449	4	个人统计	paperStatistics/personalStatistics	318	1	1
450	449	新增	insert	319	2	1
451	449	修改	update	320	2	1
452	449	删除	updateIsSystem	321	2	1
453	449	详细	paperStatistics/listPaperByUserId	322	2	1
454	449	编辑	paperStatistics/exportPersonalStatistics	323	2	1
455	4	个人统计图	paperStatistics/personalStatisticsTable	326	1	1
456	455	新增	insert	327	2	1
457	455	修改	update	328	2	1
458	455	删除	updateIsSystem	329	2	1
459	455	详细	detial	330	2	1
460	455	编辑	edit	331	2	1
461	4	个人详细统计	paperStatistics/personalDetails	333	1	1
462	461	新增	insert	334	2	1
463	461	修改	update	335	2	1
464	461	删除	updateIsSystem	336	2	1
465	461	详细	paperStatistics/listPaperDetailByUserId	337	2	1
466	461	编辑	paperStatistics/exportPersonalDetails	338	2	1
467	4	单位详细统计	paperStatistics/sectorDetails	340	1	1
468	467	新增	insert	341	2	1
469	467	修改	update	342	2	1
470	467	删除	updateIsSystem	343	2	1
471	467	详细	paperStatistics/listPaperDetailBySectorId	344	2	1
472	467	编辑	paperStatistics/exportSectorDetails	345	2	1
473	329	撤销不立案	TaNotRegister/cancelProposal	167	2	1
474		信息发布		701	1	1
475	1	信息发布类型管理	SysNewsType/index	74	1	1
476	475	新增	SysNewsType/insert	75	2	1
477	475	修改	SysNewsType/update	76	2	1
478	475	删除	SysNewsType/updateIsSystem	77	2	1
479	475	详细	SysNewsType/detial	78	2	1
480	475	编辑	SysNewsType/edit	79	2	1
481	474	信息发布	SysNews/index	702	1	1
482	481	新增	SysNews/insert	703	2	1
483	481	修改	SysNews/update	704	2	1
484	481	删除	SysNews/updateIsSystem	705	2	1
485	481	详细	SysNews/detial	706	2	1
486	481	编辑	SysNews/edit	707	2	1
559		履职数据统计		709	1	1
563	559	履职情况汇总表	ViewStatisticsInformation/index	710	1	1
564	563	查询	ViewStatisticsInformation/listStatisticsInformation	711	2	1
565	563	导出	ViewStatisticsInformation/exportExcel	712	2	1
566	559	履职人员汇总情况	ViewStatisticsInformation/indexUser	714	1	1
567	6	用户管理	SysUser/indexValue?isCommittee=1	3081	1	1
568	567	新增	SysUser/insert	3082	2	1
569	567	修改	SysUser/update	3083	2	1
570	567	删除	SysUser/updateIsSystem	3084	2	1
571	567	详细	SysUser/detial	3085	2	1
572	567	编辑	SysUser/editVo	3086	2	1
622	3	稿件处理	SqmyPaper/sd_approvalPaper		1	0
623	3	稿件处理（单流程）	SqmyPaper/sd_approvalPaper		1	0
624	4	报送市政协统计	SdSqmyApproval/sd_cityStatistics	347	1	1
625	624	新增	SdSqmyApproval/sd_cityStatisticsinsert	348	2	1
626	624	修改	SdSqmyApproval/sd_cityStatisticsupdate	349	2	1
627	624	删除	SdSqmyApproval/sd_cityStatisticsupdateIsSystem	350	2	1
628	624	详细	SdSqmyApproval/sd_cityStatisticsdetial	351	2	1
629	624	编辑	SdSqmyApproval/sd_cityStatisticsedit	352	2	1
630	4	《社情民意反映》统计	SdSqmyApproval/sd_sqmyFeedback	353	1	1
631	4	各单位积分明细表	SdSqmyApproval/sd_sectorDetail	354	1	1
632	631	新增	SdSqmyApproval/sd_sectorDetailinsert	355	2	1
633	631	修改	SdSqmyApproval/sd_sectorDetailupdate	356	2	1
634	631	删除	SdSqmyApproval/sd_sectorDetailupdateIsSystem	357	2	1
635	631	详细	SdSqmyApproval/sd_sectorDetaildetial	358	2	1
636	631	编辑	SdSqmyApproval/sd_sectorDetailedit	359	2	1
638	5	组联室		1862	1	1
639	5	办公室		1935	1	1
640	5	专委会		2007	1	1
641	5	街镇		2044	1	1
642	5	综合室		2111	1	1
643	5	提案室		2167	1	1
644	638	推荐参加各类会议	LvzhiInformationRegistration/index?projectId=1	1863	1	1
645	644	新增	LvzhiInformationRegistration/insert	1864	2	1
646	644	修改	LvzhiInformationRegistration/update	1865	2	1
647	644	删除	LvzhiInformationRegistration/updateIsSystem	1866	2	1
648	644	详细	LvzhiInformationRegistration/detial	1867	2	1
649	644	编辑	LvzhiInformationRegistration/edit	1868	2	1
650	638	推荐参加各类活动登记	LvzhiInformationRegistration/index?projectId=2	1874	1	1
651	650	新增	LvzhiInformationRegistration/insert	1875	2	1
652	650	修改	LvzhiInformationRegistration/update	1876	2	1
653	650	删除	LvzhiInformationRegistration/updateIsSystem	1877	2	1
654	650	详细	LvzhiInformationRegistration/detial	1878	2	1
655	650	编辑	LvzhiInformationRegistration/edit	1879	2	1
656	638	其他活动登记	LvzhiInformationRegistration/index?projectId=3	1883	1	1
657	656	新增	LvzhiInformationRegistration/insert	1884	2	1
658	656	修改	LvzhiInformationRegistration/update	1885	2	1
659	656	删除	LvzhiInformationRegistration/updateIsSystem	1886	2	1
660	656	详细	LvzhiInformationRegistration/detial	1887	2	1
661	656	编辑	LvzhiInformationRegistration/edit	1888	2	1
662	638	全会发言登记	LvzhiInformationMinutes/index?projectId=4	1892	1	1
663	662	新增	LvzhiInformationMinutes/insert	1893	2	1
664	662	修改	LvzhiInformationMinutes/update	1894	2	1
665	662	删除	LvzhiInformationMinutes/updateIsSystem	1895	2	1
666	662	详细	LvzhiInformationMinutes/detial	1896	2	1
667	662	编辑	LvzhiInformationMinutes/edit	1897	2	1
668	638	其他加分	LvzhiBonusPoints/index?projectId=5	1901	1	1
669	668	新增	LvzhiBonusPoints/insert	1902	2	1
670	668	修改	LvzhiBonusPoints/update	1903	2	1
671	668	删除	LvzhiBonusPoints/updateIsSystem	1904	2	1
672	668	详细	LvzhiBonusPoints/detial	1905	2	1
673	668	编辑	LvzhiBonusPoints/edit	1906	2	1
674	638	参加有关单位会议或活动	LvzhiInformationRegistration/index?projectId=6	1908	1	1
675	674	新增	LvzhiInformationRegistration/insert	1909	2	1
676	674	修改	LvzhiInformationRegistration/update	1910	2	1
677	674	删除	LvzhiInformationRegistration/updateIsSystem	1911	2	1
678	674	详细	LvzhiInformationRegistration/detial	1912	2	1
679	674	编辑	LvzhiInformationRegistration/edit	1913	2	1
680	638	市政协考评登记	LvzhiInformationRegistration/indexProject?projectId=7	1917	1	1
681	680	新增	LvzhiInformationRegistration/insert	1918	2	1
682	680	修改	LvzhiInformationRegistration/update	1919	2	1
683	680	删除	LvzhiInformationRegistration/updateIsSystem	1920	2	1
684	680	详细	LvzhiInformationRegistration/detial	1921	2	1
685	680	编辑	LvzhiInformationRegistration/edit	1922	2	1
686	639	政协全会登记	LvzhiInformationRegistration/indexProject?projectId=8	1936	1	1
687	686	新增	LvzhiInformationRegistration/insert	1937	2	1
688	686	修改	LvzhiInformationRegistration/update	1938	2	1
689	686	删除	LvzhiInformationRegistration/updateIsSystem	1939	2	1
690	686	详细	LvzhiInformationRegistration/detial	1940	2	1
691	686	编辑	LvzhiInformationRegistration/edit	1941	2	1
692	639	政协常委会议登记	LvzhiInformationRegistration/index?projectId=9	1945	1	1
693	692	新增	LvzhiInformationRegistration/insert	1946	2	1
694	692	修改	LvzhiInformationRegistration/update	1947	2	1
695	692	删除	LvzhiInformationRegistration/updateIsSystem	1948	2	1
696	692	详细	LvzhiInformationRegistration/detial	1949	2	1
697	692	编辑	LvzhiInformationRegistration/edit	1950	2	1
698	639	政协全会会议发言登记	LvzhiInformationRegistration/index?projectId=10	1954	1	1
699	698	新增	LvzhiInformationRegistration/insert	1955	2	1
700	698	修改	LvzhiInformationRegistration/update	1956	2	1
701	698	删除	LvzhiInformationRegistration/updateIsSystem	1957	2	1
702	698	详细	LvzhiInformationRegistration/detial	1958	2	1
703	698	编辑	LvzhiInformationRegistration/edit	1959	2	1
704	639	政协全会	LvzhiInformationRegistration/index?projectId=11	1962	1	1
705	704	新增	LvzhiInformationRegistration/insert	1963	2	1
706	704	修改	LvzhiInformationRegistration/update	1964	2	1
707	704	删除	LvzhiInformationRegistration/updateIsSystem	1965	2	1
708	704	详细	LvzhiInformationRegistration/detial	1966	2	1
709	704	编辑	LvzhiInformationRegistration/edit	1967	2	1
710	639	常委会议	LvzhiInformationRegistration/index?projectId=12	1971	1	1
711	710	新增	LvzhiInformationRegistration/insert	1972	2	1
712	710	修改	LvzhiInformationRegistration/update	1973	2	1
713	710	删除	LvzhiInformationRegistration/updateIsSystem	1974	2	1
714	710	详细	LvzhiInformationRegistration/detial	1975	2	1
715	710	编辑	LvzhiInformationRegistration/edit	1976	2	1
716	639	参加委员之家发表	LvzhiInformationRegistration/index?projectId=13	1980	1	1
717	716	新增	LvzhiInformationRegistration/insert	1981	2	1
718	716	修改	LvzhiInformationRegistration/update	1982	2	1
719	716	删除	LvzhiInformationRegistration/updateIsSystem	1983	2	1
720	716	详细	LvzhiInformationRegistration/detial	1984	2	1
721	716	编辑	LvzhiInformationRegistration/edit	1985	2	1
722	639	组织或参加扶贫济困活动	LvzhiInformationRegistration/indexAiding?projectId=14	1989	1	1
723	722	新增	LvzhiInformationRegistration/insert	1990	2	1
724	722	修改	LvzhiInformationRegistration/update	1991	2	1
725	722	删除	LvzhiInformationRegistration/updateIsSystem	1992	2	1
726	722	详细	LvzhiInformationRegistration/detial	1993	2	1
727	722	编辑	LvzhiInformationRegistration/edit	1994	2	1
728	639	政协活动登记	LvzhiInformationRegistration/index?projectId=15	1998	1	1
729	728	新增	LvzhiInformationRegistration/insert	1999	2	1
730	728	修改	LvzhiInformationRegistration/update	2000	2	1
731	728	删除	LvzhiInformationRegistration/updateIsSystem	2001	2	1
732	728	详细	LvzhiInformationRegistration/detial	2002	2	1
733	728	编辑	LvzhiInformationRegistration/edit	2003	2	1
734	640	专委会会议登记	LvzhiInformationRegistration/index?projectId=16	2008	1	1
735	734	新增	LvzhiInformationRegistration/insert	2009	2	1
736	734	修改	LvzhiInformationRegistration/update	2010	2	1
737	734	删除	LvzhiInformationRegistration/updateIsSystem	2011	2	1
738	734	详细	LvzhiInformationRegistration/detial	2012	2	1
739	734	编辑	LvzhiInformationRegistration/edit	2013	2	1
740	640	组织或参加扶贫济困活动	LvzhiInformationRegistration/indexAiding?projectId=17	2017	1	1
741	740	新增	LvzhiInformationRegistration/insert	2018	2	1
742	740	修改	LvzhiInformationRegistration/update	2019	2	1
743	740	删除	LvzhiInformationRegistration/updateIsSystem	2020	2	1
744	740	详细	LvzhiInformationRegistration/detial	2021	2	1
745	740	编辑	LvzhiInformationRegistration/edit	2022	2	1
746	640	协助举办相关活动	LvzhiInformationRegistration/index?projectId=18	2026	1	1
747	746	新增	LvzhiInformationRegistration/insert	2027	2	1
748	746	修改	LvzhiInformationRegistration/update	2028	2	1
749	746	删除	LvzhiInformationRegistration/updateIsSystem	2029	2	1
750	746	详细	LvzhiInformationRegistration/detial	2030	2	1
751	746	编辑	LvzhiInformationRegistration/edit	2031	2	1
752	640	专委会活动登记	LvzhiInformationRegistration/index?projectId=19	2035	1	1
753	752	新增	LvzhiInformationRegistration/insert	2036	2	1
754	752	修改	LvzhiInformationRegistration/update	2037	2	1
755	752	删除	LvzhiInformationRegistration/updateIsSystem	2038	2	1
756	752	详细	LvzhiInformationRegistration/detial	2039	2	1
757	752	编辑	LvzhiInformationRegistration/edit	2040	2	1
758	641	街镇会议登记	LvzhiInformationRegistration/index?projectId=20	2045	1	1
759	758	新增	LvzhiInformationRegistration/insert	2046	2	1
760	758	修改	LvzhiInformationRegistration/update	2047	2	1
761	758	删除	LvzhiInformationRegistration/updateIsSystem	2048	2	1
762	758	详细	LvzhiInformationRegistration/detial	2049	2	1
763	758	编辑	LvzhiInformationRegistration/edit	2050	2	1
764	641	街镇活动登记	LvzhiInformationRegistration/index?projectId=21	2054	1	1
765	764	新增	LvzhiInformationRegistration/insert	2055	2	1
766	764	修改	LvzhiInformationRegistration/update	2056	2	1
767	764	删除	LvzhiInformationRegistration/updateIsSystem	2057	2	1
768	764	详细	LvzhiInformationRegistration/detial	2058	2	1
769	764	编辑	LvzhiInformationRegistration/edit	2059	2	1
770	641	组织或参加扶贫济困活动	LvzhiInformationRegistration/indexAiding?projectId=22	2063	1	1
771	770	新增	LvzhiInformationRegistration/insert	2064	2	1
772	770	修改	LvzhiInformationRegistration/update	2065	2	1
773	770	删除	LvzhiInformationRegistration/updateIsSystem	2066	2	1
774	770	详细	LvzhiInformationRegistration/detial	2067	2	1
775	770	编辑	LvzhiInformationRegistration/edit	2068	2	1
776	641	市政协考评登记	LvzhiInformationRegistration/indexProject?projectId=23	2073	1	1
777	776	新增	LvzhiInformationRegistration/insert	2074	2	1
778	776	修改	LvzhiInformationRegistration/update	2075	2	1
779	776	删除	LvzhiInformationRegistration/updateIsSystem	2076	2	1
780	776	详细	LvzhiInformationRegistration/detial	2077	2	1
781	776	编辑	LvzhiInformationRegistration/edit	2078	2	1
782	641	推荐参加各类会议、活动	LvzhiInformationRegistration/index?projectId=24	2083	1	1
783	782	新增	LvzhiInformationRegistration/insert	2084	2	1
784	782	修改	LvzhiInformationRegistration/update	2085	2	1
785	782	删除	LvzhiInformationRegistration/updateIsSystem	2086	2	1
786	782	详细	LvzhiInformationRegistration/detial	2087	2	1
787	782	编辑	LvzhiInformationRegistration/edit	2088	2	1
788	641	接受新闻媒体采访	LvzhiInformationRegistration/indexProject?projectId=25	2092	1	1
789	788	新增	LvzhiInformationRegistration/insert	2093	2	1
790	788	修改	LvzhiInformationRegistration/update	2094	2	1
791	788	删除	LvzhiInformationRegistration/updateIsSystem	2095	2	1
792	788	详细	LvzhiInformationRegistration/detial	2096	2	1
793	788	编辑	LvzhiInformationRegistration/edit	2097	2	1
794	641	协助举办相关活动	LvzhiInformationRegistration/index?projectId=26	2102	1	1
795	794	新增	LvzhiInformationRegistration/insert	2103	2	1
796	794	修改	LvzhiInformationRegistration/update	2104	2	1
797	794	删除	LvzhiInformationRegistration/updateIsSystem	2105	2	1
798	794	详细	LvzhiInformationRegistration/detial	2106	2	1
799	794	编辑	LvzhiInformationRegistration/edit	2107	2	1
800	642	综合室会议登记	LvzhiInformationRegistration/index?projectId=27	2112	1	1
801	800	新增	LvzhiInformationRegistration/insert	2113	2	1
802	800	修改	LvzhiInformationRegistration/update	2114	2	1
803	800	删除	LvzhiInformationRegistration/updateIsSystem	2115	2	1
804	800	详细	LvzhiInformationRegistration/detial	2116	2	1
805	800	编辑	LvzhiInformationRegistration/edit	2117	2	1
806	642	综合室活动登记	LvzhiInformationRegistration/index?projectId=28	2121	1	1
807	806	新增	LvzhiInformationRegistration/insert	2122	2	1
808	806	修改	LvzhiInformationRegistration/update	2123	2	1
809	806	删除	LvzhiInformationRegistration/updateIsSystem	2124	2	1
810	806	详细	LvzhiInformationRegistration/detial	2125	2	1
811	806	编辑	LvzhiInformationRegistration/edit	2126	2	1
812	642	协助举办相关活动	LvzhiInformationRegistration/index?projectId=29	2130	2	1
813	812	新增	LvzhiInformationRegistration/insert	2131	2	1
814	812	修改	LvzhiInformationRegistration/update	2132	2	1
815	812	删除	LvzhiInformationRegistration/updateIsSystem	2133	2	1
816	812	详细	LvzhiInformationRegistration/detial	2134	2	1
817	812	编辑	LvzhiInformationRegistration/edit	2135	2	1
818	642	为群众或社会办实事	LvzhiInformationRegistration/index?projectId=30	2139	1	1
819	818	新增	LvzhiInformationRegistration/insert	2140	2	1
820	818	修改	LvzhiInformationRegistration/update	2141	2	1
821	818	删除	LvzhiInformationRegistration/updateIsSystem	2142	2	1
822	818	详细	LvzhiInformationRegistration/detial	2143	2	1
823	818	编辑	LvzhiInformationRegistration/edit	2144	2	1
824	642	接受新闻媒体采访	LvzhiInformationRegistration/indexProject?projectId=31	2148	1	1
825	824	新增	LvzhiInformationRegistration/insert	2149	2	1
826	824	修改	LvzhiInformationRegistration/update	2150	2	1
827	824	删除	LvzhiInformationRegistration/updateIsSystem	2151	2	1
828	824	详细	LvzhiInformationRegistration/detial	2152	2	1
829	824	编辑	LvzhiInformationRegistration/edit	2153	2	1
830	642	开展调研、书面提出意见、撰写文章	LvzhiInformationRegistration/index?projectId=32	2158	1	1
831	830	新增	LvzhiInformationRegistration/insert	2159	2	1
832	830	修改	LvzhiInformationRegistration/update	2160	2	1
833	830	删除	LvzhiInformationRegistration/updateIsSystem	2161	2	1
834	830	详细	LvzhiInformationRegistration/detial	2162	2	1
835	830	编辑	LvzhiInformationRegistration/edit	2163	2	1
836	643	提案室会议登记	LvzhiInformationRegistration/index?projectId=33	2168	1	1
837	836	新增	LvzhiInformationRegistration/insert	2169	2	1
838	836	修改	LvzhiInformationRegistration/update	2170	2	1
839	836	删除	LvzhiInformationRegistration/updateIsSystem	2171	2	1
840	836	详细	LvzhiInformationRegistration/detial	2172	2	1
841	836	编辑	LvzhiInformationRegistration/edit	2173	2	1
842	643	提案室活动登记	LvzhiInformationRegistration/index?projectId=34	2177	1	1
843	842	新增	LvzhiInformationRegistration/insert	2178	2	1
844	842	修改	LvzhiInformationRegistration/update	2179	2	1
845	842	删除	LvzhiInformationRegistration/updateIsSystem	2180	2	1
846	842	详细	LvzhiInformationRegistration/detial	2181	2	1
847	842	编辑	LvzhiInformationRegistration/edit	2182	2	1
848	6	委员个人管理	SysUser/userEdit	3090	2	1
849	6	履职信息修改审核	SysUser/userList	3095	2	1
851	638	协助开展活动	LvzhiInformationRegistration/index?projectId=64	1926	1	1
852	851	新增	LvzhiInformationRegistration/insert	1927	2	1
853	851	修改	LvzhiInformationRegistration/update	1928	2	1
854	851	删除	LvzhiInformationRegistration/updateIsSystem	1929	2	1
855	851	详细	LvzhiInformationRegistration/detial	1930	2	1
856	851	编辑	LvzhiInformationRegistration/edit	1931	2	1
857	1	流程节点管理	SysBatchableNode/index	81	1	1
858	857	新增	SysBatchableNode/insert	82	2	1
859	857	修改	SysBatchableNode/update	83	2	1
860	857	删除	SysBatchableNode/updateIsSystem	84	2	1
861	857	详细	SysBatchableNode/detial	85	2	1
862	857	编辑	SysBatchableNode/edit	86	2	1
863	1	积分稿酬管理	SysRewardStandard/index	88	1	1
864	863	新增	SysRewardStandard/insert	89	2	1
865	863	修改	SysRewardStandard/update	90	2	1
866	863	删除	SysRewardStandard/updateIsSystem	91	2	1
867	863	详细	SysRewardStandard/detial	92	2	1
868	863	编辑	SysRewardStandard/edit	93	2	1
869	1	紧急程度	SysEmerge/index	95	1	1
870	869	新增	SysEmerge/insert	96	2	1
871	869	修改	SysEmerge/update	97	2	1
872	869	删除	SysEmerge/updateIsSystem	98	2	1
873	869	详细	SysEmerge/detial	99	2	1
874	869	编辑	SysEmerge/edit	100	2	1
875	1	刊类管理	SysIssueCategory/index	102	1	1
876	875	新增	SysIssueCategory/insert	103	2	1
877	875	修改	SysIssueCategory/update	104	2	1
878	875	删除	SysIssueCategory/updateIsSystem	105	2	1
879	875	详细	SysIssueCategory/detial	106	2	1
880	875	编辑	SysIssueCategory/edit	107	2	1
881	1	信息员管理	SysUser/indexMessenger	4593	1	1
882	881	新增	SysMessenger/insert	4594	2	1
883	881	修改	SysMessenger/update	4595	2	1
884	881	删除	SysMessenger/updateIsSystem	4596	2	1
885	881	详细	SysMessenger/detial	4597	2	1
886	881	编辑	SysMessenger/edit	4598	2	1
887	4	信息员统计	paperStatistics/messengerStatistics	360	1	1
888	887	新增	paperStatistics/personalStatisticsinsert	361	2	1
889	887	修改	paperStatistics/personalStatisticsupdate	362	2	1
890	887	删除	paperStatistics/personalStatisticsupdateIsSystem	363	2	1
891	887	详细	paperStatistics/personalStatisticsdetial	364	2	1
892	887	编辑	paperStatistics/personalStatisticsedit	365	2	1
893	2	历史提案	TaProposalHistory/index	217	1	1
894	293	列表	TaMyProposal/listMyProposal	122	2	1
895	293	查询当前用户信息	/TaMyProposal/getCurrentUserInfo	123	2	1
896	287	列表查询	SqmyPaper/listMine	226	2	1
897	377	查询常用意见	SqmyOpinion/list	235	2	1
898	323	列表	TaProposalRegister/listRegisterProposal	160	2	1
899	377	查看呈批表	SqmyApproval/listApproval	236	2	1
900	377	撤回处理结果	SqmyApproval/recall	237	2	1
901	377	列表查询	SqmyPaper/listHandle	238	2	1
902	287	稿件取回	SqmyPaper/updateIsSystem	227	2	1
903	329	列表	TaNotRegister/listNotRegisterProposal	168	2	1
904	377	稿件发送	SqmyApproval/handlePaper	239	2	1
905	359	列表	proposalUpload/listUploadData	203	2	1
906	893	列表	TaProposalHistory/listHistoryProposal	210	2	1
907	383	列表	SqmyApproval/listDispatchPaper	244	2	1
908	341	列表	TaProposalHistory/listHistoryProposal	218	2	1
909	299	列表	TaProposalPeriod/listAccessPeriod	130	2	1
910	305	列表	TaProposalReview/listReviewProposal	137	2	1
911	383	稿件采用	SqmyPaper/adoptPaper	245	2	1
912	311	列表	TaProposalReview/listReviewFirstProposal	144	2	1
913	311	编辑	TaProposalReview/cancelProposal	145	2	1
914	317	列表	TaProposalRegister/listRegisterProposal	152	2	1
915	317	编辑	TaProposalRegister/cancelProposal	153	2	1
916	335	列表	TaReturnProposal/listReturnProposal	175	2	1
917	335	编辑	TaProposalExport/exportReturn-proposal	176	2	1
918	389	稿件编印详情	SqmyBatchable/edit	253	2	1
919	365	列表	TaStatistics/by-statistics	183	2	1
920	353	列表	proposalUpload/listUploadData	196	2	1
921	419	列表	TaProposalHistory/listViewOtherProposal	288	2	1
922	425	列表	paperStatistics/listSector	295	2	1
923	425	查看	test/wordNtk	296	2	1
924	431	列表	paperStatistics/listSector	303	2	1
925	437	列表	paperStatistics/listInfoCategory	310	2	1
926	443	列表	paperStatistics/committeeAndMessengerStatistics	317	2	1
927	449	列表	paperStatistics/listPersonalStatistics	324	2	1
928	449	查看	test/wordNtk	325	2	1
929	455	列表	paperStatistics/listPersonalStatistics	332	2	1
930	461	列表	paperStatistics/listPersonalDetails	339	2	1
931	467	列表	paperStatistics/listSectorDetails	346	2	1
932	287	查看呈批表	SqmyApproval/listApproval	228	2	1
933	848	列表	ViewStatisticsInformation/listStatisticsUserAllInformation	3091	2	1
934	567	列表	ViewStatisticsInformation/listStatisticsUserInformation	3087	2	1
935	58	列表	ViewStatisticsInformation/listStatisticsUserInformation	3078	2	1
936	58	汇总	ViewStatisticsInformation/listStatisticsUserAllInformation	3079	2	1
937	58	导出	ViewStatisticsInformation/exportExcelUserAll	3080	2	1
938	567	列表	SysUser/listAllVo	3088	2	1
939	849	列表	SysUser/listUserAllVo	3096	2	1
940	481	列表	SysNews/list	708	2	1
941	563	列表	TLvzhiProjectType/listGridDate	713	2	1
942	566	列表	SysUser/listGroup	715	2	1
943	848	详细	SysUser/editVoUser	3092	2	1
944	849	编辑	SysUser/editVoUser	3097	2	1
945	7	列表	SysPermission/list	8	2	1
946	13	列表	SysRole/list	15	2	1
947	19	列表	SysRole/list	19	2	1
948	19	角色权限列表	SysPermissionRole/list	20	2	1
949	22	列表	SysGroup/list	27	2	1
950	34	列表	SysUnitduty/list	40	2	1
951	52	列表	LvzhiPerformanceProject/list	59	2	1
952	275	列表	SysParty/list	66	2	1
953	281	列表	SqmyCategory/list	73	2	1
954	475	列表	SysNewsType/list	80	2	1
955	857	列表	SysBatchableNode/listReviewPerson	87	2	1
956	863	列表	SysRewardStandard/list	94	2	1
957	869	列表	SysEmerge/list	101	2	1
958	875	列表	SysIssueCategory/list	108	2	1
959	377	流程节点列表	SqmyBatchableNode/list	240	2	1
960	383	列表	SqmyApproval/listResponsePaper	246	2	1
961	401	流程节点列表	SqmyBatchableNode/list	266	2	1
962	644	列表	LvzhiInformationRegistration/list	1869	2	1
963	650	列表	LvzhiInformationRegistration/list	1880	2	1
964	656	列表	LvzhiInformationRegistration/list	1889	2	1
965	662	列表	LvzhiInformationMinutes/list	1898	2	1
966	668	列表	LvzhiBonusPoints/list	1907	2	1
967	674	列表	LvzhiInformationRegistration/list	1914	2	1
968	680	编辑	SysUser/listAllVo	1923	2	1
969	680	列表	LvzhiInformationRegistration/listAiding	1924	2	1
970	851	列表	LvzhiInformationRegistration/list	1932	2	1
971	686	列表	LvzhiInformationRegistration/list	1942	2	1
972	692	列表	LvzhiInformationRegistration/list	1951	2	1
973	704	列表	LvzhiInformationRegistration/list	1968	2	1
974	710	列表	LvzhiInformationRegistration/list	1977	2	1
975	716	列表	LvzhiInformationRegistration/list	1986	2	1
976	722	列表	LvzhiInformationRegistration/listAiding	1995	2	1
977	728	列表	LvzhiInformationRegistration/list	2004	2	1
978	734	列表	LvzhiInformationRegistration/list	2014	2	1
979	740	列表	LvzhiInformationRegistration/listAiding	2023	2	1
980	746	列表	LvzhiInformationRegistration/list	2032	2	1
981	752	列表	LvzhiInformationRegistration/list	2041	2	1
982	758	列表	LvzhiInformationRegistration/list	2051	2	1
983	764	列表	LvzhiInformationRegistration/list	2060	2	1
984	770	编辑	SysUser/listAllVo	2069	2	1
985	770	列表	LvzhiInformationRegistration/listAiding	2070	2	1
986	776	编辑	SysUser/listAllVo	2079	2	1
987	776	列表	LvzhiInformationRegistration/listAiding	2080	2	1
988	782	列表	LvzhiInformationRegistration/list	2089	2	1
989	788	编辑	SysUser/listAllVo	2098	2	1
990	788	列表	LvzhiInformationRegistration/listAiding	2099	2	1
991	794	列表	LvzhiInformationRegistration/list	2108	2	1
992	800	列表	LvzhiInformationRegistration/list	2118	2	1
993	806	列表	LvzhiInformationRegistration/list	2127	2	1
994	812	列表	LvzhiInformationRegistration/list	2136	2	1
995	818	列表	LvzhiInformationRegistration/list	2145	2	1
996	824	编辑	SysUser/listAllVo	2154	2	1
997	824	列表	LvzhiInformationRegistration/listAiding	2155	2	1
998	830	列表	LvzhiInformationRegistration/list	2164	2	1
999	836	列表	LvzhiInformationRegistration/list	2174	2	1
1000	842	列表	LvzhiInformationRegistration/list	2183	2	1
1001	644	编辑列表	LvzhiInformationRegistrationMx/list	1870	2	1
1002	644	人员列表	LvzhiInformationRegistration/editVo	1871	2	1
1003	680	更新	LvzhiInformationRegistration/updateAiding	1925	2	1
1004	644	人员列表	LvzhiInformationRegistrationMx/listUser	1872	2	1
1005	644	人员添加	LvzhiInformationRegistrationMx/updateBatch	1873	2	1
1006	650	人员列表	LvzhiInformationRegistrationMx/listUser	1881	2	1
1007	650	人员添加	LvzhiInformationRegistrationMx/updateBatch	1882	2	1
1008	656	人员列表	LvzhiInformationRegistrationMx/listUser	1890	2	1
1009	656	人员添加	LvzhiInformationRegistrationMx/updateBatch	1891	2	1
1010	662	人员列表	LvzhiInformationRegistrationMx/listUser	1899	2	1
1011	662	人员添加	LvzhiInformationRegistrationMx/updateBatch	1900	2	1
1012	674	人员列表	LvzhiInformationRegistrationMx/listUser	1915	2	1
1013	674	人员添加	LvzhiInformationRegistrationMx/updateBatch	1916	2	1
1014	851	人员列表	LvzhiInformationRegistrationMx/listUser	1933	2	1
1015	851	人员添加	LvzhiInformationRegistrationMx/updateBatch	1934	2	1
1016	692	人员列表	LvzhiInformationRegistrationMx/listUser	1952	2	1
1017	692	人员添加	LvzhiInformationRegistrationMx/updateBatch	1953	2	1
1018	698	人员列表	LvzhiInformationRegistrationMx/listUser	1960	2	1
1019	698	人员添加	LvzhiInformationRegistrationMx/updateBatch	1961	2	1
1020	704	人员列表	LvzhiInformationRegistrationMx/listUser	1969	2	1
1021	704	人员添加	LvzhiInformationRegistrationMx/updateBatch	1970	2	1
1022	710	人员列表	LvzhiInformationRegistrationMx/listUser	1978	2	1
1023	710	人员添加	LvzhiInformationRegistrationMx/updateBatch	1979	2	1
1024	716	人员列表	LvzhiInformationRegistrationMx/listUser	1987	2	1
1025	716	人员添加	LvzhiInformationRegistrationMx/updateBatch	1988	2	1
1026	728	人员列表	LvzhiInformationRegistrationMx/listUser	2005	2	1
1027	728	人员添加	LvzhiInformationRegistrationMx/updateBatch	2006	2	1
1028	395	列表	SqmyPaper/list	260	2	1
1029	734	人员列表	LvzhiInformationRegistrationMx/listUser	2015	2	1
1030	734	人员添加	LvzhiInformationRegistrationMx/updateBatch	2016	2	1
1031	746	人员列表	LvzhiInformationRegistrationMx/listUser	2033	2	1
1032	746	人员添加	LvzhiInformationRegistrationMx/updateBatch	2034	2	1
1033	752	人员列表	LvzhiInformationRegistrationMx/listUser	2042	2	1
1034	752	人员添加	LvzhiInformationRegistrationMx/updateBatch	2043	2	1
1035	758	人员列表	LvzhiInformationRegistrationMx/listUser	2052	2	1
1036	758	人员添加	LvzhiInformationRegistrationMx/updateBatch	2053	2	1
1037	764	人员列表	LvzhiInformationRegistrationMx/listUser	2061	2	1
1038	764	人员添加	LvzhiInformationRegistrationMx/updateBatch	2062	2	1
1039	782	人员列表	LvzhiInformationRegistrationMx/listUser	2090	2	1
1040	782	人员添加	LvzhiInformationRegistrationMx/updateBatch	2091	2	1
1041	794	人员列表	LvzhiInformationRegistrationMx/listUser	2109	2	1
1042	794	人员添加	LvzhiInformationRegistrationMx/updateBatch	2110	2	1
1043	800	人员列表	LvzhiInformationRegistrationMx/listUser	2119	2	1
1044	800	人员添加	LvzhiInformationRegistrationMx/updateBatch	2120	2	1
1045	806	人员列表	LvzhiInformationRegistrationMx/listUser	2128	2	1
1046	806	人员添加	LvzhiInformationRegistrationMx/updateBatch	2129	2	1
1047	812	人员列表	LvzhiInformationRegistrationMx/listUser	2137	2	1
1048	812	人员添加	LvzhiInformationRegistrationMx/updateBatch	2138	2	1
1049	818	人员列表	LvzhiInformationRegistrationMx/listUser	2146	2	1
1050	818	人员添加	LvzhiInformationRegistrationMx/updateBatch	2147	2	1
1051	830	人员列表	LvzhiInformationRegistrationMx/listUser	2165	2	1
1052	830	人员添加	LvzhiInformationRegistrationMx/updateBatch	2166	2	1
1053	836	人员列表	LvzhiInformationRegistrationMx/listUser	2175	2	1
1054	836	人员添加	LvzhiInformationRegistrationMx/updateBatch	2176	2	1
1055	842	人员列表	LvzhiInformationRegistrationMx/listUser	2184	2	1
1056	842	人员添加	LvzhiInformationRegistrationMx/updateBatch	2185	2	1
1058	686	列表	LvzhiInformationRegistration/listAiding	1943	2	1
1059	686	更新	LvzhiInformationRegistration/updateAiding	1944	2	1
1060	722	列表	LvzhiInformationRegistration/listAiding	1996	2	1
1061	722	更新	LvzhiInformationRegistration/updateAiding	1997	2	1
1062	740	列表	LvzhiInformationRegistration/listAiding	2024	2	1
1063	740	更新	LvzhiInformationRegistration/updateAiding	2025	2	1
1064	770	列表	LvzhiInformationRegistration/listAiding	2071	2	1
1065	770	更新	LvzhiInformationRegistration/updateAiding	2072	2	1
1066	776	列表	LvzhiInformationRegistration/listAiding	2081	2	1
1067	776	更新	LvzhiInformationRegistration/updateAiding	2082	2	1
1068	788	列表	LvzhiInformationRegistration/listAiding	2100	2	1
1069	788	更新	LvzhiInformationRegistration/updateAiding	2101	2	1
1070	824	列表	LvzhiInformationRegistration/listAiding	2156	2	1
1071	824	更新	LvzhiInformationRegistration/updateAiding	2157	2	1
1072	287	编辑-获取作者	SqmyPaper/getCurrentUserInfo	2909	2	1
1073	857	修改审核人	SysBatchableNode/updateBatchableUser	2922	2	1
1074	881	编辑人员	SysUser/editVo	4599	2	1
1075	881	列表	SysUser/listAllVo	4600	2	1
1076	377	打开word	SqmyPaper/word		2	0
1077	377	流程节点用户列表	SqmyBatchableNodeUser/list		2	0
1078	383	打开word	SqmyPaper/word		2	0
1079	389	查询最大刊期	SqmyBatchable/getMaxIssue	2933	2	1
1080	401	流程节点用户列表	SqmyBatchableNodeUser/list	2934	2	1
1081	848	新增	SysUser/updateUser	3093	2	1
1082	848	导出	ViewStatisticsInformation/exportExcelUserAll	3094	2	1
1083	567	导出	SysUser/exportExcel	3089	2	1
1084	849	编辑	SysUser/updatelUser	3098	2	1
1085	287	人员列表	SysUser/listUser		2	0
1086	317	发通知	TaProposalRegister/PostProposal		2	0
1087	329	发通知	TaNotRegister/PostProposal		2	0
1088	335	发通知	TaReturnProposal/PostProposal		2	0
1089	293	查看通知书	TaInform/edit		2	0
1090	341	详细列表	TaProposalRegister/listRegisterProposal		2	0
1091	341	按id查询	TaProposalReview/SelectProposalById		2	0
1092	293	下载通知书	TaInform/ProposalInfoByperson		2	0
1093	323	移动排序	TaProposalRegister/updateRowNo		2	0
1094	317	修改案号	TaProposalRegister/updateProposalNo		2	0
1095	317	移动排序	TaProposalRegister/updateRowNo		2	0
1096	341	设置可看	TaProposalPeriod/setReadable		2	0
1097	341	目录导出	TaProposalExport/export-current-register-proposal		2	0
1098	407	word查询稿件内容	SqmyPaper/editVoWord		2	0
1099	449	word查看稿件内容	SqmyPaper/editVoWord		2	0
1100	881	修改	SysUser/update	4601	2	1
1101	881	删除	SysUser/updateIsSystem	4602	2	1
1102	377	常用意见删除	SqmyOpinion/delete		2	0
1103	293	下载附件	TaMyProposal/downloadData		2	0
1104	305	下载附件	TaMyProposal/downloadData		2	0
1105	293	导出word	TaProposalExport/exportProposal		2	0
1106	305	导出word	TaProposalExport/exportProposal		2	0
1107	329	导出excel	TaProposalExport/exportNotRegister-proposal		2	0
1108	644	委员批量删除	LvzhiInformationRegistrationMx/deleteBatch		2	0
1109	347	查询	TaStatistics/by-infomation		2	0
1110	644	人员发言	LvzhiInformationMinutes/updateMx		2	0
1111	317	保存案号	TaProposalRegister/updataProposalNo		2	0
1112	7	权限刷新	SysRole/updataRedisPermission	3146	2	1
1113	395	合并稿件	SqmyReward/mergePaper		2	0
1114	395	取消合并	SqmyReward/cancelMergePaper		2	0
1115	377	导出word	SqmyPaper/exportWord		2	0
1116	341	目录说明	TaStatistics/directoryHead		2	0
1117	7	缓存刷新	/SysPermission/clean		2	0
1118	6	委员活动申诉	LvzhiAppeal/index	4584	3	1
1119	1118	列表	LvzhiAppeal/list	4585	3	1
1120	1118	新增	LvzhiAppeal/insert	4586	3	1
1121	1118	修改	LvzhiAppeal/update	4587	3	1
1122	1118	删除	LvzhiAppeal/updateIsSystem	4588	3	1
1123	1118	详细	LvzhiAppeal/detial	4589	3	1
1124	1118	编辑	LvzhiAppeal/edit	4590	3	1
1125	2	组别提案情况	TaProposalHistory/listProposalByGroupIndex		3	0
1126	1125	列表	TaProposalHistory/listProposalByGrouplist		3	0
1127	1125	新增	TaProposalHistory/listProposalByGroupinsert		3	0
1128	1125	修改	TaProposalHistory/listProposalByGroupupdate		3	0
1129	1125	删除	TaProposalHistory/listProposalByGroupupdateIsSystem		3	0
1130	1125	详细	TaProposalHistory/listProposalByGroupdetial		3	0
1131	1125	编辑	TaProposalHistory/listProposalByGroupedit		3	0
1132	1125	查看提案	TaMyProposal/SelectProposalById		2	0
1133	567	获取用户信息	SysUser/listAllUserVo	4591	2	1
1134	881	获取信息员列表	SysUser/listAllMessengerVo	4603	2	1

## 改代码

> https://blog.csdn.net/yrbwxt/article/details/83312765
>
> 复制数据库到本地，因本地的mysql为5.5，与cppcc的mysql5.7不相符，有数据项属性问题，所以将mysql5.7下载并设置默认端口为3307

### 修改：

* template/index.html

  ```
  <a class="proposal_normal_a" onclick="ind_addTab('../TaMyProposal/index2','我的提案')" title="我的提案">
  ```

* 创建index2.html