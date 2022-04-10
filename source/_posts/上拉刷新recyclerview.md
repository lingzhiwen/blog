---
title: 上拉刷新，下拉加载的recyclerView
---


### 1.上拉刷新的布局文件swipe_header_view
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="40dp"
    android:gravity="center">
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:gravity="center"
        android:orientation="horizontal">
        <ProgressBar
            android:id="@+id/progressbar"
            style="?android:attr/progressBarStyleSmallInverse"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginStart="10dp"
            android:gravity="center"
            android:text="下拉刷新" />
    </LinearLayout>
</FrameLayout>
```


### 2.下拉加载的布局文件swipe_footer_view
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="40dp"
    android:gravity="center">
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:gravity="center"
        android:orientation="horizontal">
        <ProgressBar
            android:id="@+id/progressbar"
            style="?android:attr/progressBarStyleSmallInverse"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
        <TextView
            android:id="@+id/footer_loading"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginStart="10dp"
            android:gravity="center"
            android:text="加载中..." />
    </LinearLayout>
</FrameLayout>
```



### 3.下拉刷新，上拉加载的recyclerview

```
import android.content.Context;
import android.content.res.Resources;
import android.util.AttributeSet;
import android.util.Log;
import android.view.Gravity;
import android.view.MotionEvent;
import android.view.View;
import android.widget.LinearLayout;
import android.widget.Scroller;

import androidx.annotation.Nullable;
import androidx.recyclerview.widget.RecyclerView;
import androidx.recyclerview.widget.RecyclerView.Adapter;

import org.jetbrains.annotations.NotNull;


public final class RecyclerRefreshLayout extends LinearLayout {
    @NotNull
    private RecyclerView contentRv;
    private final Scroller scroller;
    @NotNull
    private View headerView;
    @NotNull
    private View footerView;
    private boolean isHeaderViewEnable;
    private boolean isFooterViewEnable;
    @Nullable
    private RecyclerRefreshLayout.ActionListener actionListener;
    private final int HEADER_HEIGHT;
    private final int FOOTER_HEIGHT;
    private final int MAX_SCROLL_DISTANCE;
    private int layoutHeight;
    private int layoutWidth;
    private boolean isLoadData;
    private float downY;
    private float lastY;
    private boolean rvIsArriveTop;
    private boolean rvIsArriveDown;
    private static final String TAG = "SwipeLayout";
    private static final int ANIM_DURATION = 300;
    private static final float SCROLL_DISTANCE = 80.0F;
    private float downX;
    private long currentTimeMillis;

    public RecyclerRefreshLayout(@NotNull Context ctx, @NotNull AttributeSet attr) {
        super(ctx, attr);
        this.contentRv = new RecyclerView(ctx);
        this.scroller = new Scroller(this.getContext());
        this.headerView = View.inflate(this.getContext(), R.layout.swipe_header_view, null);
        this.footerView = View.inflate(this.getContext(), R.layout.swipe_footer_view, null);
        this.isHeaderViewEnable = true;
        this.isFooterViewEnable = true;
        HEADER_HEIGHT = dp2px(ctx, SCROLL_DISTANCE);
        FOOTER_HEIGHT = dp2px(ctx, SCROLL_DISTANCE);
        MAX_SCROLL_DISTANCE = dp2px(ctx, SCROLL_DISTANCE);
        this.setGravity(Gravity.CENTER);
        this.setOrientation(VERTICAL);
        contentRv.setOverScrollMode(RecyclerView.OVER_SCROLL_NEVER);
        this.post(() -> {
            layoutHeight = getHeight();
            layoutWidth = getWidth();
            addView(getHeaderView(), (new LayoutParams(LayoutParams.MATCH_PARENT, HEADER_HEIGHT)));
            addView(getContentRv(), (new LayoutParams(LayoutParams.MATCH_PARENT, LayoutParams.MATCH_PARENT)));
            /** 一开始的时候要让Header和Footer看不见，设置向上的负paddingTop，paddingBottom */
            setPadding(0, -HEADER_HEIGHT / 2, 0, -FOOTER_HEIGHT / 2);
            addView(getFooterView(), (new LayoutParams(LayoutParams.MATCH_PARENT, FOOTER_HEIGHT)));
        });
    }

    @NotNull
    public final RecyclerView getContentRv() {
        return this.contentRv;
    }

    public final void setContentRv(@NotNull RecyclerView var1) {
        this.contentRv = var1;
    }

    @NotNull
    public final View getHeaderView() {
        return this.headerView;
    }

    public final void setHeaderView(@NotNull View value) {
        this.removeView(this.headerView);
        this.headerView = value;
        this.addView(this.headerView, 0, new LayoutParams(LayoutParams.MATCH_PARENT, this.HEADER_HEIGHT));
    }

    @NotNull
    public final View getFooterView() {
        return this.footerView;
    }

    public final void setFooterView(@NotNull View value) {
        this.removeView(this.footerView);
        this.footerView = value;
        this.addView(this.footerView, -1, (LayoutParams) (new LayoutParams(LayoutParams.MATCH_PARENT, this.FOOTER_HEIGHT)));
    }

    public final boolean isHeaderViewEnable() {
        return this.isHeaderViewEnable;
    }

    public final void setHeaderViewEnable(boolean var1) {
        this.isHeaderViewEnable = var1;
    }

    public final boolean isFooterViewEnable() {
        return this.isFooterViewEnable;
    }

    public final void setFooterViewEnable(boolean var1) {
        this.isFooterViewEnable = var1;
    }

    @Nullable
    public final RecyclerRefreshLayout.ActionListener getActionListener() {
        return this.actionListener;
    }

    public final void setActionListener(@Nullable RecyclerRefreshLayout.ActionListener var1) {
        this.actionListener = var1;
    }

    public boolean dispatchTouchEvent(@Nullable MotionEvent ev) {
        switch (ev.getAction()) {
            case MotionEvent.ACTION_DOWN:
                this.downX = ev.getX();
                this.downY = ev.getY();
                this.lastY = ev.getY();
                break;
            case MotionEvent.ACTION_MOVE:
                int disX = (int) Math.abs(ev.getX() - downX);
                int disY = (int) Math.abs(ev.getY() - downY);
                this.rvIsArriveTop = this.isArriveTop(this.contentRv);
                this.rvIsArriveDown = this.isArriveBottom(this.contentRv);
                /** 只允许纵向下拉刷新，解决嵌套viewpager滑动冲突。 */
                if (!this.isLoadData && disY > disX) {
                    this.scrollLayout(ev.getY());
                }
                this.lastY = ev.getY();
                break;
            case MotionEvent.ACTION_UP:
                int scrollY = this.getScrollY();
                if (Math.abs(scrollY) >= this.MAX_SCROLL_DISTANCE / 2 & !this.isLoadData) {
                    this.isLoadData = true;
                    if (this.rvIsArriveTop) {
                        this.startRefresh();
                    } else if (this.rvIsArriveDown) {
                        this.startLoadMore();
                    }
                } else {
                    this.smoothResetScroll();
                }
                break;

            case MotionEvent.ACTION_CANCEL:
                break;
        }
        return super.dispatchTouchEvent(ev);

    }

    private final void scrollLayout(float moveY) {
        float nextScrollY = (float) this.getScrollY() + this.lastY - moveY;
        if (this.rvIsArriveTop) {
            this.pullDownScroll(nextScrollY);
        } else if (this.rvIsArriveDown) {
            this.pullUpScroll(nextScrollY);
        }
    }

    private final void pullDownScroll(float nextScrollY) {
        if (this.isHeaderViewEnable) {
            if (nextScrollY >= (float) 0) {
                this.scrollTo(0, 0);
            } else if (nextScrollY < (float) (-this.MAX_SCROLL_DISTANCE)) {
                this.scrollTo(0, -this.MAX_SCROLL_DISTANCE);
            } else {
                this.scrollTo(0, (int) nextScrollY);
            }
        }
    }

    private final void pullUpScroll(float nextScrollY) {
        if (this.isFooterViewEnable) {
            if (nextScrollY < (float) 0) {
                this.scrollTo(0, 0);
            } else if (nextScrollY > (float) this.MAX_SCROLL_DISTANCE) {
                this.scrollTo(0, this.MAX_SCROLL_DISTANCE);
            } else {
                this.scrollTo(0, (int) nextScrollY);
            }
        }
    }

    public final void startRefresh() {
        if (this.isHeaderViewEnable) {
            this.isLoadData = true;
            this.contentRv.smoothScrollToPosition(0);
            this.scroller.startScroll(0, this.getScrollY(), 0, -this.MAX_SCROLL_DISTANCE - this.getScrollY());
            if (this.actionListener != null) {
                long diffTime = System.currentTimeMillis() - currentTimeMillis;
                if (diffTime > 200) {
                    this.actionListener.onRefresh();
                }
                currentTimeMillis = System.currentTimeMillis();
            }
            this.invalidate();
        }
    }

    public final void startLoadMore() {
        if (!isFooterViewEnable) return;
        this.isLoadData = true;
        Adapter adapter = this.contentRv.getAdapter();
        if (adapter == null) {
            return;
        }
        int itemCount = adapter.getItemCount();
        this.contentRv.smoothScrollToPosition(itemCount > 0 ? itemCount - 1 : 0);
        this.scroller.startScroll(0, this.getScrollY(), 0, this.MAX_SCROLL_DISTANCE - this.getScrollY());
        if (this.actionListener != null) {
            this.actionListener.onLoadMore();
        }
        this.invalidate();
    }

    public final void smoothResetScroll() {
        this.scroller.startScroll(0, this.getScrollY(), 0, -this.getScrollY(), ANIM_DURATION);
        this.isLoadData = false;
        this.invalidate();
    }

    private final boolean isArriveTop(RecyclerView rv) {
        return !rv.canScrollVertically(-1);
    }

    private final boolean isArriveBottom(RecyclerView rv) {
        return !rv.canScrollVertically(1);
    }

    private final int dp2px(Context ctx, float dpValue) {
        Resources resources = ctx.getApplicationContext().getResources();
        float scale = resources.getDisplayMetrics().density;
        return (int) (dpValue * scale + 0.5F);
    }

    public void computeScroll() {
        if (this.scroller.computeScrollOffset()) {
            this.scrollTo(0, this.scroller.getCurrY());
            this.postInvalidate();
        }
    }

    public interface ActionListener {
        void onRefresh();

        void onLoadMore();
    }
}
```