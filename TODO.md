## 框架

开发语言：Java Spring

	上传服务

	存储服务

	转码服务

	播放服务

	CDN分发
	



## 接口

* 增删改查

	上传初始化，返回upload_id，
	用Redis存储upload表，上传成功后删除
	`video/upload/init?fn=Base64(abc.mp4)&fsize=1000000&psize=500&hash=asdfasdf&公共参数`
	参数：
		fn file_name 文件名
		fsize file_size 文件大小
		psize part_size 分段大小
		auto auto_transcode 是否自动转码
		hash hash(md5)（可选，用于秒传）


	返回：
		文件不存在，返回upload_id
		upload_id
		status
		md5
		size

		文件已存在，返回vid

	上传数据，上传结束成功则返回vid
	`video/upload/upload?upid=1234&pseq=1`
	参数：
		upid upload_id
		pseq part_sequence_number 分段序号（1-10000），同个upload_id，唯一标识分段，同时标识在文件内的相对位置
		hash hash(md5)(可选)

	除最后一段外，分段数据大小（Content-Length)应为part_size

	服务端验证文件是否上传完整，验证：分段数量和数据总大小，分段hash和合并后hash

	返回：
		vid


	断点续传，查询断点位置
	`video/upload/resume?upid=1234`
	
	整段上传：
			Content-Range:

		分段上传：
			直接从缺失分段开始发送

	返回：
		整段上传，返回断点offset；
		分段上传，返回缺失分段号列表
		upload_id不存在，已完成、失效、或错误。
		已完成，返回vid。

	查询视频媒体信息
	`video/mediainfo?vid=123`
	参数：
		vid 视频id
	返回：
		视频媒体信息

	删除视频
	`video/delete?vid=123`
	参数：
		vid 视频id
	返回：
		成功，失败

	创建转码任务
	`video/transcode?vid=123`


	创建转码任务
	`video/transcode?src=${source_uri}&dst=${dest_uri}`
	
	返回：
		vid

	获取视频状态，不存在，上传中，上传成功，上传失败，转码排队中，转码中，转码成功，转码失败，可播放
	`video/status?vid=123`


	获取播放地址
	`video/playurl?vid=123`

	获取视频截图
	`video/snapshot?vid=123`

	重新转码
	`video/retransocde?vid=123`


* API签名验证（安全机制）

		app_key
		app_secret
		random
		signature


## 数据库

	CREATE TABLE video
	(
		id BIGINT NOT NULL PRIMARY KEY AUTO_INCREMENT COMMENT '主键，媒体文件id',
		parent_id BIGINT COMMENT '源文件为空，转码后文件为对应源文件id',
		unique_id BIGINT COMMENT '相同hash文件id',
		source_name VCHAR(2048) NOT NULL COMMENT '原始文件名',
		create_time DATETIME NOT NULL COMMENT '创建时间',
		update_time DATETIME NOT NULL COMMENT '更新时间',
		file_size BITINT NOT NULL COMMENT '文件大小',
		video_width INT COMMENT '视频宽度', 
		video_height INT COMMENT '视频高度', 
		duration INT COMMENT '总时长',
		hash VCHAR(1024) COMMENT '文件hash值',
		//String encSig;			//文件加密Key
		definition INT COMMENT '清晰度',
		storage_uri VCHAR(4096) NOT NULL COMMENT '云存储地址',
		status INT COMMENT '状态'
		image_index INT COMMENT '截图第几张',
		image_count INT COMMENT '截图数量',
		task_priority INT COMMENT '转码任务优先级'
	)

	CREATE TABLE video_snapshot
	(
		id
		video_id
		image_index
	)

## 存储
	云存储 阿里云 OSS 

## 转码
	

## CDN
	网宿、蓝汛、云帆、星域
	服务功能完备、特点
	成本





