#   BSD LICENSE
#
#   Copyright 2015 6WIND S.A.
#   Copyright 2015 Mellanox.
#
#   Redistribution and use in source and binary forms, with or without
#   modification, are permitted provided that the following conditions
#   are met:
#
#     * Redistributions of source code must retain the above copyright
#       notice, this list of conditions and the following disclaimer.
#     * Redistributions in binary form must reproduce the above copyright
#       notice, this list of conditions and the following disclaimer in
#       the documentation and/or other materials provided with the
#       distribution.
#     * Neither the name of 6WIND S.A. nor the names of its
#       contributors may be used to endorse or promote products derived
#       from this software without specific prior written permission.
#
#   THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
#   "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
#   LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
#   A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
#   OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
#   SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
#   LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
#   DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
#   THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
#   (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
#   OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

include $(RTE_SDK)/mk/rte.vars.mk

# Library name.
LIB = librte_pmd_mlx5.a

# Sources.
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_rxq.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_txq.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_rxtx.c
ifneq ($(filter y,$(CONFIG_RTE_ARCH_X86_64) \
		  $(CONFIG_RTE_ARCH_ARM64)),)
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_rxtx_vec.c
endif
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_trigger.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_ethdev.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_mac.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_rxmode.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_vlan.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_stats.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_rss.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_mr.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_flow.c
SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD) += mlx5_socket.c

# Basic CFLAGS.
CFLAGS += -O3
CFLAGS += -std=c11 -Wall -Wextra
CFLAGS += -g
CFLAGS += -I.
CFLAGS += -D_BSD_SOURCE
CFLAGS += -D_DEFAULT_SOURCE
CFLAGS += -D_XOPEN_SOURCE=600
CFLAGS += $(WERROR_FLAGS)
CFLAGS += -Wno-strict-prototypes
LDLIBS += -libverbs -lmlx5
LDLIBS += -lrte_eal -lrte_mbuf -lrte_mempool -lrte_ring
LDLIBS += -lrte_ethdev -lrte_net -lrte_kvargs
LDLIBS += -lrte_bus_pci

# A few warnings cannot be avoided in external headers.
CFLAGS += -Wno-error=cast-qual

EXPORT_MAP := rte_pmd_mlx5_version.map
LIBABIVER := 1

# DEBUG which is usually provided on the command-line may enable
# CONFIG_RTE_LIBRTE_MLX5_DEBUG.
ifeq ($(DEBUG),1)
CONFIG_RTE_LIBRTE_MLX5_DEBUG := y
endif

# User-defined CFLAGS.
ifeq ($(CONFIG_RTE_LIBRTE_MLX5_DEBUG),y)
CFLAGS += -pedantic -UNDEBUG -DPEDANTIC
else
CFLAGS += -DNDEBUG -UPEDANTIC
endif

ifdef CONFIG_RTE_LIBRTE_MLX5_TX_MP_CACHE
CFLAGS += -DMLX5_PMD_TX_MP_CACHE=$(CONFIG_RTE_LIBRTE_MLX5_TX_MP_CACHE)
endif

include $(RTE_SDK)/mk/rte.lib.mk

# Generate and clean-up mlx5_autoconf.h.

export CC CFLAGS CPPFLAGS EXTRA_CFLAGS EXTRA_CPPFLAGS
export AUTO_CONFIG_CFLAGS = -Wno-error

ifndef V
AUTOCONF_OUTPUT := >/dev/null
endif

mlx5_autoconf.h.new: FORCE

mlx5_autoconf.h.new: $(RTE_SDK)/buildtools/auto-config-h.sh
	$Q $(RM) -f -- '$@'
	$Q sh -- '$<' '$@' \
		HAVE_IBV_DEVICE_VXLAN_SUPPORT \
		infiniband/verbs.h \
		enum IBV_DEVICE_VXLAN_SUPPORT \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_IBV_WQ_FLAG_RX_END_PADDING \
		infiniband/verbs.h \
		enum IBV_WQ_FLAG_RX_END_PADDING \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_IBV_MLX5_MOD_MPW \
		infiniband/mlx5dv.h \
		enum MLX5DV_CONTEXT_FLAGS_MPW_ALLOWED \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_IBV_MLX5_MOD_CQE_128B_COMP \
		infiniband/mlx5dv.h \
		enum MLX5DV_CONTEXT_FLAGS_CQE_128B_COMP \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_ETHTOOL_LINK_MODE_25G \
		/usr/include/linux/ethtool.h \
		enum ETHTOOL_LINK_MODE_25000baseCR_Full_BIT \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_ETHTOOL_LINK_MODE_50G \
		/usr/include/linux/ethtool.h \
		enum ETHTOOL_LINK_MODE_50000baseCR2_Full_BIT \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_ETHTOOL_LINK_MODE_100G \
		/usr/include/linux/ethtool.h \
		enum ETHTOOL_LINK_MODE_100000baseKR4_Full_BIT \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_IBV_DEVICE_COUNTERS_SET_SUPPORT \
		infiniband/verbs.h \
		type 'struct ibv_counter_set_init_attr' \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_SUPPORTED_40000baseKR4_Full \
		/usr/include/linux/ethtool.h \
		define SUPPORTED_40000baseKR4_Full \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_SUPPORTED_40000baseCR4_Full \
		/usr/include/linux/ethtool.h \
		define SUPPORTED_40000baseCR4_Full \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_SUPPORTED_40000baseSR4_Full \
		/usr/include/linux/ethtool.h \
		define SUPPORTED_40000baseSR4_Full \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_SUPPORTED_40000baseLR4_Full \
		/usr/include/linux/ethtool.h \
		define SUPPORTED_40000baseLR4_Full \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_SUPPORTED_56000baseKR4_Full \
		/usr/include/linux/ethtool.h \
		define SUPPORTED_56000baseKR4_Full \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_SUPPORTED_56000baseCR4_Full \
		/usr/include/linux/ethtool.h \
		define SUPPORTED_56000baseCR4_Full \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_SUPPORTED_56000baseSR4_Full \
		/usr/include/linux/ethtool.h \
		define SUPPORTED_56000baseSR4_Full \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_SUPPORTED_56000baseLR4_Full \
		/usr/include/linux/ethtool.h \
		define SUPPORTED_56000baseLR4_Full \
		$(AUTOCONF_OUTPUT)
	$Q sh -- '$<' '$@' \
		HAVE_STATIC_ASSERT \
		/usr/include/assert.h \
		define static_assert \
		$(AUTOCONF_OUTPUT)

# Create mlx5_autoconf.h or update it in case it differs from the new one.

mlx5_autoconf.h: mlx5_autoconf.h.new
	$Q [ -f '$@' ] && \
		cmp '$<' '$@' $(AUTOCONF_OUTPUT) || \
		mv '$<' '$@'

$(SRCS-$(CONFIG_RTE_LIBRTE_MLX5_PMD):.c=.o): mlx5_autoconf.h

clean_mlx5: FORCE
	$Q rm -f -- mlx5_autoconf.h mlx5_autoconf.h.new

clean: clean_mlx5
