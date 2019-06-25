�����æTiDB�Ĳ�Ʒ�����ڼ���binlog���ܵ�ʱ����Ҫ��binlogctl����ִ����������TiDB��������������ִ�еķ���ֵ��Ȼ����pd-ctl������������json��ʽ������һ��������־һ�������ݸ�ʽ���������޷�ʹ��json.Decode�����ŵĻ�ԭ���ݵĽṹ�����ظ�ʽ���£�
```
$./binlogctl -pd-urls=http://127.0.0.1:2379 -cmd generate_meta
INFO[0000] [pd] create pd client with endpoints [http://192.168.199.118:32379]
INFO[0000] [pd] leader switches to: http://192.168.199.118:32379, previous:
INFO[0000] [pd] init cluster id 6569368151110378289
2018/06/21 11:24:47 meta.go:117: [info] meta: &{CommitTS:400962745252184065}
```

�������������meta��Ϣ��Ȼ����������**key:value**�Ľṹ��ʽ����ˣ�ģ��encoding/json���ʵ�֣�Ҳ�����ŵĽ�������־��ʽ��ԭ��structʵ����

##### ��һ�����õ�meta������Ч���ݣ�
```
func GetMeta(session, scriptPath, pdAddr string) (Meta, error) {
	genCmd := fmt.Sprintf("%s/binlogctl -pd-urls=http://%s -cmd generate_meta", scriptPath, pdAddr)
	log.Debug(session, "get binlog meta",
		log.Fields{
			"scriptPath": scriptPath,
			"pdAddr":     pdAddr,
			"genCmd":     genCmd,
		})

	cmd := exec.Command("sh", "-c", genCmd)

	meta := Meta{}
	output, err := cmd.CombinedOutput()
	if err != nil {
		log.Error(session, "get binlog meta error", log.Fields{
			"cmd":    cmd,
			"output": output,
			"err":    err,
		})
		return meta, err
	}

	lines := strings.Split(string(output), "\n")
	for _, line := range lines {
		if strings.Index(line, "meta") != -1 {
			err := decode(line, &meta)
			if err != nil {
				log.Error(session, "decode meta faild", log.Fields{
					"cmd":  cmd,
					"line": line,
					"err":  err,
				})

				return meta, err
			}
		}
	}

	return meta, nil
}
```

##### �ڶ���������reflectʵ��decode
```
type Meta struct {
	CommitTS string
}

func decode(str string, v interface{}) error {
	vt := reflect.TypeOf(v).Elem()
	vv := reflect.ValueOf(v).Elem()

	var globalErr error
	// ����struct�ĳ�Ա
	fmt.Print(vt.NumField())
	for i := 0; i < vt.NumField(); i++ {
		field := vt.Field(i)

		// ��ȡ�ַ���str��field��Ӧ��ֵ
		value := getValue(str, field.Name)

		// ��ȡreflect.Value
		target := vv.FieldByName(field.Name)
		if target.Kind() != reflect.String {
			panic("Field of struct should be string(type)!")
		} else {
			// ������������ֵ��ֵ�������interface
			target.SetString(value)
		}
	}

	defer func() {
		if err := recover(); err != nil {
			globalErr = fmt.Errorf("%v", err)
		}
	}()

	return globalErr
}
```
�˴�����ֻ����struct��ԱΪһ��Ľṹ�����ṹΪstring����

##### ��������ȡ��struct�ĳ�Ա����Ӧ��ֵ
```
func getValue(str, key string) string {
	if len(str) == 0 ||
		len(key) == 0 {
		return ""
	}

	// str:"INFO[0000] meta: &{CommitTS: 409184142902689793}"
	// index := strings.Index(str, key)
	// new := str[index :]
	// indexSplitA := strings.Index(new, ":")
	// indexSplitB := strings.Index(new, ",") || indexSplitB := strings.Index(new, "}")
	// value := new[indexSplitA + 1 : indexSplitB]
	// TrimSpace(value)
	value := str[strings.Index(str, key):]
	if index := strings.Index(value, ","); index != -1 {
		return strings.TrimSpace(value[len(key)+1 : index])
	} else {
		return strings.TrimSpace(value[len(key)+1 : strings.Index(value, "}")])
	}
}
```
�˴�Ϊ�˽�ʡ����ʹ������Ƭ�����׶����ɲο�ע��

��������reflectΪʲô����goӵ��һЩ��̬���Ե����ԣ���ҿ���ȥ��Դ�룬������򵥵Ľ���һ��ԭ��reflect��ֱ��go���Եĺ��ţ�����go���Ե�������Ʊ�¶�˳���������ѭ�Ŵ�����һ�飺
```
reflect/type.go

// TypeOf returns the reflection Type that represents the dynamic type of i.
// If i is a nil interface value, TypeOf returns nil.
func TypeOf(i interface{}) Type {
	eface := *(*emptyInterface)(unsafe.Pointer(&i))
	return toType(eface.typ)
}
```
�������Ķ����ָ�뱻ǿת��emptyInterface��Ϊʲô��ǿת�����ǿ���emptyInterface��ʲô��
```
reflect/value.go

// emptyInterface is the header for an interface{} value.
type emptyInterface struct {
	typ  *rtype
	word unsafe.Pointer
}
```
rtype����ʲô......
```
reflect/type.go

// rtype is the common implementation of most values.
// It is embedded in other struct types.
//
// rtype must be kept in sync with ../runtime/type.go:/^type._type.
type rtype struct {
	size       uintptr
	ptrdata    uintptr  // number of bytes in the type that can contain pointers
	hash       uint32   // hash of type; avoids computation in hash tables
	tflag      tflag    // extra type information flags
	align      uint8    // alignment of variable with this type
	fieldAlign uint8    // alignment of struct field with this type
	kind       uint8    // enumeration for C
	alg        *typeAlg // algorithm table
	gcdata     *byte    // garbage collection data
	str        nameOff  // string form
	ptrToThis  typeOff  // type for pointer to this type, may be zero
}
```
�����rtype must be kept in sync with ../runtime/type.go:/^type._type
```
runtime/type.go

type _type struct {
	size       uintptr
	ptrdata    uintptr // size of memory prefix holding all pointers
	hash       uint32
	tflag      tflag
	align      uint8
	fieldalign uint8
	kind       uint8
	alg        *typeAlg
	// gcdata stores the GC type data for the garbage collector.
	// If the KindGCProg bit is set in kind, gcdata is a GC program.
	// Otherwise it is a ptrmask bitmap. See mbitmap.go for details.
	gcdata    *byte
	str       nameOff
	ptrToThis typeOff
}
```
���ˣ�һ�������;ͳ����ˣ������͵ĸ�����Ա����һһ�����������ǲ��Ǹо���python��object������Զ���