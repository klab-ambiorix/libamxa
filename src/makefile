include ../makefile.inc

# build destination directories
OBJDIR = ../output/$(MACHINE)

# TARGETS
TARGET_SO = $(OBJDIR)/$(COMPONENT).so.$(VERSION)
TARGET_A = $(OBJDIR)/$(COMPONENT).a

# directories
# source directories
SRCDIR = .
INCDIR_PUB = ../include 
INCDIR_PRIV = ../include_priv 
INCDIRS = $(INCDIR_PUB) $(INCDIR_PRIV) $(if $(STAGINGDIR), $(STAGINGDIR)/include) $(if $(STAGINGDIR), $(STAGINGDIR)/usr/include)
STAGING_LIBDIR = $(if $(STAGINGDIR), -L$(STAGINGDIR)/lib) $(if $(STAGINGDIR), -L$(STAGINGDIR)/usr/lib)

# files
HEADERS = $(wildcard $(INCDIR_PUB)/acl/*.h)
SOURCES = $(wildcard $(SRCDIR)/*.c)
OBJECTS = $(addprefix $(OBJDIR)/,$(notdir $(SOURCES:.c=.o)))
		
# compilation and linking flags
CFLAGS += -Werror -Wall -Wextra \
          -Wformat=2 -Wshadow \
          -Wwrite-strings -Wredundant-decls \
          -Wno-format-nonliteral \
          -g3 -Wmissing-declarations $(addprefix -I ,$(INCDIRS)) \
          -fPIC -pthread

ifeq ($(CC_NAME),g++)
    CFLAGS += -std=c++2a
else
	CFLAGS += -Wstrict-prototypes \
	          -Wold-style-definition \
			  -Wnested-externs \
	 		  -Wdeclaration-after-statement \
			  -std=gnu11
endif

LDFLAGS += $(STAGING_LIBDIR) -shared -fPIC -lamxc -lamxj -lyajl -lamxd -lamxb

# targets
all: $(TARGET_SO) $(TARGET_A)

$(TARGET_SO): $(OBJECTS)
	$(CC) -Wl,-soname,$(COMPONENT).so.$(VMAJOR) -o $(@) $(OBJECTS) $(LDFLAGS)

$(TARGET_A): $(OBJECTS)
	$(AR) rcs $(@) $(OBJECTS)

-include $(OBJECTS:.o=.d)

$(OBJDIR)/%.o: $(SRCDIR)/%.c | $(OBJDIR)/
	$(CC) $(CFLAGS) -c -o $@ $<
	@$(CC) $(CFLAGS) -MM -MP -MT '$(@) $(@:.o=.d)' -MF $(@:.o=.d) $(<)

$(OBJDIR)/:
	$(MKDIR) -p $@
	
clean:
	rm -rf ../output/ ../$(COMPONENT)-*.* ../$(COMPONENT)_*.*

.PHONY: all clean
