##################################################
# Makefile
##################################################

# Programs, flags, etc.
ASM		= nasm
CC		= gcc
LD		= ld
ASMFLAGS	= -f elf
CFLAGS		= -c -m32
LDFLAGS		= -s -m elf_i386

BOOT:=boot.asm
LDR:=loader.asm
KERNEL:=kernel.asm
BOOT_BIN:=$(subst .asm,.bin,$(BOOT))
LDR_BIN:=$(subst .asm,.bin,$(LDR))
KERNEL_BIN:=$(subst .asm,.bin,$(KERNEL))

IMG:=a.img
FLOPPY:=/mnt

.PHONY : everything

everything : $(BOOT_BIN) $(LDR_BIN) $(KERNEL_BIN) $(IMG)
	dd if=$(BOOT_BIN) of=$(IMG) bs=512 count=1 conv=notrunc
	sudo mount -o loop $(IMG) $(FLOPPY)
	sudo cp $(LDR_BIN) $(FLOPPY) -v
	sudo cp $(KERNEL_BIN) $(FLOPPY) -v
	sudo umount $(FLOPPY)

clean :
	rm -f $(BOOT_BIN) $(LDR_BIN) $(KERNEL_BIN) *.o $(IMG)

$(BOOT_BIN) : $(BOOT)
	$(ASM) $(ASMFLAGS) $< -o $@

$(LDR_BIN) : $(LDR)
	$(ASM) $(ASMFLAGS) $< -o $@

$(KERNEL_BIN) : $(KERNEL)
	$(ASM) $(ASMFLAGS) -o $(subst .asm,.o,$(KERNEL)) $<
	$(LD) $(LDFLAGS) -o $@ $(subst .asm,.o,$(KERNEL))

$(IMG) : $(IMG).gz 
	gzip -cd $< > $@

run : everything
	qemu-system-i386 -fda $(IMG) -boot a
