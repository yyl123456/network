
1、IO_URING 是一个异步编程框架，可以认为是提供给应用层使用的一个网络编程框架，一如开发经常使用epoll,select,poll等。它更多的是向上层提供一套更高效的网络通信接口。
2、DPDK：DPDK属于用户层程序可以直接处理网络数据的一种框架，它可以略过大多数的和内核交互的过程。DPDK的优秀之处已经在应用中体现出来，但是DPDK的不足也同样暴露了出来，
3、XDP、TC（eBPF）则可以认为是与DPDK不同的另外一种方案，DPDK是尽量减少和内核的交互，直接连接用户层和网络数据（驱动或网卡），而XDP等则在仍然与内核保持较强的联系





socket + io_ring

server_fd = socket(AF_INET, SOCK_STREAM, 0);
server_addr.sin_family = AF_INET;
server_addr.sin_addr.s_addr = INADDR_ANY;
server_addr.sin_port = htons(8080);
bind(server_fd, (struct sockaddr *)&server_addr, sizeof(server_addr));
listen(server_fd, 5);
  io_uring_queue_init(QUEUE_DEPTH, &ring, 0);
accept(server_fd, (struct sockaddr *)&client_addr, &client_len);
  sqe = io_uring_get_sqe(&ring);
  io_uring_prep_recv(sqe, client_fd, buffer, BUFFER_SIZE, 0);
  sqe->user_data = 1;
  io_uring_submit(&ring);
  io_uring_wait_cqe(&ring, &cqe);
printf("Received %d bytes: %s\n", cqe->res, buffer);
  io_uring_cqe_seen(&ring, cqe);


socket

server_fd = socket(AF_INET, SOCK_STREAM, 0);
server_addr.sin_family = AF_INET;
server_addr.sin_addr.s_addr = INADDR_ANY;
server_addr.sin_port = htons(8080);
bind(server_fd, (struct sockaddr *)&server_addr, sizeof(server_addr));
listen(server_fd, 5);
accept(server_fd, (struct sockaddr *)&client_addr, &client_len);
  read(client_fd, buffer, BUFFER_SIZE);
  printf("Received %d bytes: %s\n", ret, buffer);


