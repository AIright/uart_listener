# uart_listener
UartListener class

class UartListener(Thread):
    def __init__(self, device):
        self.data = None
        self.kill = 0
        self.input = None
        self.uart = serial.Serial(device["com"], 115200, timeout=0)
        super(UartListener, self).__init__()

    def run(self) -> None:
        self.data = self.uart.read(1)
        while not self.kill:
            if self.input:
                logger.info("UART: INPUT: {0}".format(self.input))
                self.uart.write(self.input)
                self.input = None
            self.data += self.uart.read(self.uart.inWaiting())
        self.data = None
        self.uart.close()
        
 
 def test_uart_input(device):
    uart = UartListener(device)
    uart.start()
    uart.input = b'\nls\n'
    sleep(2)
    logger.info("UART: OUTPUT: {0}".format(uart.data))
    uart.kill = 1
