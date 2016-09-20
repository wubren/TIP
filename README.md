# TIP
TCP/IP protocol  for MCU

#How to use
3. 应用实例
3.1 TCP客户端
对于TCP客户端，由到服务器一个数据后，应立即返回一个ACK给服务器。可以通过调用TIPSendACK()来发送，也可以调用TIPWrite()。因为在执行TIPWrite()时会自动加上ACK。
对于有TCP处理进程的系统，如WINDOWS，它会跟据情况自动发送ACK。但TIP是一个小系统，没有专门的TCP处理进程。不直接在TIPRead()中发送ACK是为了结省带宽。

void TCPClient()
{
	tip_socket_t socket;

	...

	if( TIPSocket(drv, &socket, port, TIP_PROTO_TCP, mem, len) != 0 )
	{
		printf("Register socket failed\n");
		while(1);
	}

	if( TIPConnect(&socket, sev_ip, sev_port, NULL) != 0)
	{
		printf("Can't connect server\n");
		continue;
	}

	TIPWrite(&socket, pbuf, len, NULL);
	TIPRead(&socket, pbuf, len, NULL);
	TIPClose(&socket);
}

3.2 TCP服务端

void TCPServ()
{
	tip_socket_t socket;

	if( TIPSocket(drv, &socket, port, TIP_PROTO_TCP, mem, len) != 0 )
	{
		printf("Register socket failed\n");
		while(1);
	}

	while(1)
	{
		if( TIPAccept(&socket) != 0 )
			continue;
		while(1)
		{
			if( TIPRead() == 0)
				break;

			...

			if( TIPWrite() == 0)
				break;
		}
	}
}

3.3 UDP client

void UDPClient()
{
	tip_socket_t   socket;
	tip_dst_info_t dst_info, dst_info2;

	if( TIPSocket(drv, &socket, port, TIP_PROTO_TCP, mem, len) != 0 )
	{
		printf("Register socket failed\n");
		while(1);
	}	

	// UDP调用TIPConnect()只是为了获取目标机器的MAC地址
	if( TIPConnect(&socket, &(dst_info.ip_adr), dst_info.port, &(dst_info.eth_adr)) != 0 )
	{
		printf("UDP connect failed\n");
		while(1);
	}

	TIPWrite(&socket, pbuf, len, &dst_info);
	TIPRead(&socket, pbuf, len, &dst_info2); // dst_info2为发送端信息，
}

3.4 UDP serve

void UDPClient()
{
	tip_socket_t   socket;
	tip_dst_info_t dst_info;

	if( TIPSocket(drv, &socket, port, TIP_PROTO_TCP, mem, len) != 0 )
	{
		printf("Register socket failed\n");
		while(1);
	}	

	TIPRead(&socket, pbuf, len, &dst_info); 
	..
	TIPWrite(&socket, pbuf, len, &dst_info);
}


