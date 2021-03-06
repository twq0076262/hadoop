# MapReduce - Mapper

主要是读取InputSplit的每一个Key,Value对并进行处理

    public class Mapper<KEYIN, VALUEIN, KEYOUT, VALUEOUT> {
        /**
         * 预处理，仅在map task启动时运行一次
         */
        protected void setup(Context context) throws  IOException, InterruptedException {
        }

        /**
         * 对于InputSplit中的每一对<key, value>都会运行一次
         */
        @SuppressWarnings("unchecked")
        protected void map(KEYIN key, VALUEIN value, Context context) throws IOException, InterruptedException {
            context.write((KEYOUT) key, (VALUEOUT) value);
        }

        /**
         * 扫尾工作，比如关闭流等
         */
        protected void cleanup(Context context) throws IOException, InterruptedException {
        }

        /**
         * map task的驱动器
         */
        public void run(Context context) throws IOException, InterruptedException {
            setup(context);
            while (context.nextKeyValue()) {
                map(context.getCurrentKey(), context.getCurrentValue(), context);
            }
            cleanup(context);
        }
    }

    public class MapContext<KEYIN, VALUEIN, KEYOUT, VALUEOUT> extends TaskInputOutputContext<KEYIN, VALUEIN, KEYOUT, VALUEOUT> {
        private RecordReader<KEYIN, VALUEIN> reader;
        private InputSplit split;

        /**
         * Get the input split for this map.
         */
        public InputSplit getInputSplit() {
            return split;
        }

        @Override
        public KEYIN getCurrentKey() throws IOException, InterruptedException {
            return reader.getCurrentKey();
        }

        @Override
        public VALUEIN getCurrentValue() throws IOException, InterruptedException {
            return reader.getCurrentValue();
        }

        @Override
        public boolean nextKeyValue() throws IOException, InterruptedException {
            return reader.nextKeyValue();
        }
    }