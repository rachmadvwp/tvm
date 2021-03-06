# Makefile Example to bundle TVM modules.
TVM_ROOT=$(shell cd ../..; pwd)
NNVM_PATH=nnvm
DMLC_CORE=${TVM_ROOT}/3rdparty/dmlc-core
PKG_CFLAGS = -std=c++14 -Oz -fPIC\
	-I${TVM_ROOT}/include\
	-I${DMLC_CORE}/include\
	-I${TVM_ROOT}/3rdparty/dlpack/include\

PKG_LDFLAGS = -L${TVM_ROOT}/build

build_dir := build

test: $(build_dir)/demo $(build_dir)/bundle.so
	$(build_dir)/demo $(build_dir)/bundle.so

$(build_dir)/demo: demo.cc
	@mkdir -p $(@D)
	$(CXX) $(PKG_CFLAGS) -o $@  $^

# Serialize our graph.json file.
$(build_dir)/graph.json.cc: $(build_dir)/graph.json
	xxd -i $^  > $@

# Serialize our params.bin file.
$(build_dir)/params.bin.cc: $(build_dir)/params.bin
	xxd -i $^  > $@

$(build_dir)/model.o $(build_dir)/graph.json $(build_dir)/params.bin: build_model.py
	python $< -o $(build_dir)

# Build our bundle against the serialized bundle.cc API, the runtime.cc API, and
# the serialized graph.json and params.bin
$(build_dir)/bundle.so: bundle.cc runtime.cc $(build_dir)/model.o $(build_dir)/graph.json.cc $(build_dir)/params.bin.cc
	@mkdir -p $(@D)
	$(CXX) $(PKG_CFLAGS) -fvisibility=hidden -o $@  $^ $(PKG_LDFLAGS) -shared

clean:
	rm -r $(build_dir)
