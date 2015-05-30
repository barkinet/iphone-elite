# Introduction #

I wrote this header for the UARTs in the S-Gold2.  More stuff is to follow as we get closer to writing a fully interactive loader.  This will be tested as I hack on the loader this weekend.  If there are problems with the UART responding outside of boot ROM mode, it might be because the peripheral region is marked as as buffered or cachable.  This is not a normal thing mind you, but I've learned not to make assumptions without checking.  That being the case, no big deal, we just need cache invalidations or to modify the MMU tables to make it uncached and unbuffered.  To be honest though, these look like physical addresses.  Anyhow, some experimentation will reveal the answer.  If you already know, please feel free to let me know.

Please note, this came from xc164\_periph\_um.pdf  I am guessing the UART IP block is probably the same though.

# Details #

```
#if !defined(_PMB8876_UART_H_)
#define _PMB8876_UART_H_

#define USART0_BASE		0xF1000000
#define USART1_BASE		0xF1800000

#define USART0_CLC		USART0_BASE
#defnie USART0_PISEL		USART0_BASE + 0x04
#define USART0_ID		USART0_BASE + 0x08
#define USART0_CON		USART0_BASE + 0x10
#define USART0_BG		USART0_BASE + 0x14
#define USART0_FDV		USART0_BASE + 0x18
#define USART0_PMW		USART0_BASE + 0x1C
#define USART0_TXB		USART0_BASE + 0x20
#define USART0_RXB		USART0_BASE + 0x24
#define USART0_ABCON		USART0_BASE + 0x30
#define USART0_ABSTAT		USART0_BASE + 0x34
#define USART0_RXFCON		USART0_BASE + 0x40
#define USART0_TXFCON		USART0_BASE + 0x44
#define USART0_FSTAT		USART0_BASE + 0x48
#define USART0_FCSTAT		USART0_BASE + 0x68
#define	USART0_ICR		USART0_BASE + 0x70

#define USART1_CLC		USART1_BASE
#defnie USART1_PISEL		USART1_BASE + 0x04
#define USART1_ID		USART1_BASE + 0x08
#define USART1_CON		USART1_BASE + 0x10
#define USART1_BG		USART1_BASE + 0x14
#define USART1_FDV		USART1_BASE + 0x18
#define USART1_PMW		USART1_BASE + 0x1C
#define USART1_TXB		USART1_BASE + 0x20
#define USART1_RXB		USART1_BASE + 0x24
#define USART1_ABCON		USART1_BASE + 0x30
#define USART1_ABSTAT		USART1_BASE + 0x34
#define USART1_RXFCON		USART1_BASE + 0x40
#define USART1_TXFCON		USART1_BASE + 0x44
#define USART1_FSTAT		USART1_BASE + 0x48
#define USART1_FCSTAT		USART1_BASE + 0x68
#define	USART1_ICR		USART1_BASE + 0x70

#define	CON_R			(1 << 15)	/* Baud rate generator run control (0: disable; 1: enable) */
#define CON_LB			(1 << 14)	/* Loopback mode (0: disable; 1: enable) */
#define CON_BRS			(1 << 13)	/* Baudrate selection (0: Pre-scaler /2; 1: Pre-scaler / 3) */
#define CON_ODD			(1 << 12)	/* Parity selection (0: even; 1: odd)  */
#define	CON_FDE			(1 << 11)	/* Fraction divider enable (0: disable; 1: enable) */
#define CON_OE			(1 << 10)	/* Overrun error flag */
#define CON_FE			(1 <<  9)	/* Framing error flag */
#define CON_PE			(1 <<  8)	/* Parity error flag */
#define CON_OEN			(1 <<  7)	/* Overrun check enable (0: ignore; 1: check) */
#define CON_FEN			(1 <<  6)	/* Framing error check (0: ignore; 1: check) */
#define CON_PEN			(1 <<  5)	/* Parity check enable (0: ignore; 1: check) */
#define CON_REN			(1 <<  4)	/* Receiver bit enable (0: disable; 1: enable) */
#define CON_STP			(1 <<  3)	/* Number of stop bits (0: 1 stop bit; 1: two stop bits) */
#define CON_MODE_MASK		(7)		/* Mask for mode control */

#define CON_8BIT_SYNC		(0)		/* 8 bit sync operation */
#define CON_8BIT_ASYNC		(1)		/* 8 bit async operation */
#define CON_8BIT_IRDA		(2)		/* 8 bit IrDA async operation */
#define CON_7BIT_PARITY_ASYNC	(3)		/* 7 bit w/ parity async operation */
#define CON_9BIT_ASYNC		(4)		/* 9 bit async operation */
#define CON_8BIT_WAKEUP		(5)		/* 8 bit w/ wake up async */
#define CON_RESERVED		(6)		/* Reserved do not use */
#define CON_8BIT_PARITY_ASYNC	(7)		/* 8 bit data w/ parity async */

#define PWM_IRPW		(1 <<  8)	/* IrDA pulse width (0: 3/16 bit time; 1: PW_VALUE) */
#define PWM_PW_VALUE_MASK	0xFF

#define ABCON_RXINV		(1 << 11)	/* Receive invert enable (0: disable; 1: enable) */
#define ABCON_TXINV		(1 << 10)	/* Transmit invert enable (0: disable; 1: enable) */
#define ABCON_ABEM_ECHO_DET	(1 <<  8)	/* Autobaud echo mode enabled during detection */
#define ABCON_ABEM_ECHO_ALWAYS	(1 <<  9)	/* Autobaud echo mode always enabled */
#define ABCON_FCDETEN		(1 <<  4)	/* Fir char of two byte frame detect */
#define ABCON_ABDETEN		(1 <<  3)	/* Autobaud detection interrupt enable (0: dis; 1: en) */
#define ABCON_ABSTEN		(1 <<  2)	/* Start of autobaud detect interrupt (0: dis; 1: en) */
#define ABCON_AUREN		(1 <<  1)	/* Auto control of CON.REN (too complex for here) */
#define ABCON_ABEN		(1 <<  0)	/* Autobaud detection enable */

#define ABSTAT_DETWAIT		(1 <<  4)	/* Autobaud detect is waiting */
#define ABSTAT_SCCDET		(1 <<  3)	/* Second character with capital letter detected */
#define ABSTAT_SCSDET		(1 <<  2)	/* Second character with small letter detected */
#define ABSTAT_FCCDET		(1 <<  1)	/* First character with capital letter detected */
#define ABSTAT_FCSDET		(1 <<  0)	/* First character with small letter detected */

#define RXFCON_RXFITL(x)	((x & 8) <<  8)	/* Receive FIFO interrupt trigger level */
#define RXFCON_RXTMEN		(1 <<  2)	/* Receive FIFO transparent mode enable */
#define RXFCON_RXFFLU		(1 <<  1)	/* Receive FIFO flush */
#define RXFCON_RXFEN		(1 <<  0)	/* Receive FIFO enable */

#define TXFCON_TXFITL		((x & 8) <<  8)	/* Transmit FIFO interrupt trigger level */
#define TXFCON_TXTMEN		(1 <<  2)	/* Transmit FIFO transparent mode enable */
#define TXFCON_TXFFLU		(1 <<  1)	/* Transmit FIFO flush */
#define TXFCON_TXFEN		(1 <<  0)	/* Transmit FIFO enable */

#define FSTAT_TXFFL		(0xF <<  8)	/* Transmit FIFO filling level mask */
#define FSTAT_RXFFL		(0xF)		/* Receive FIFO filling level mask */

#endif	/* !defined(_PMB8876_UART_H_) */
```